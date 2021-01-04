---
title: Unterstützung für Azure Table Storage in Azure Cosmos DB
description: Hier erfahren Sie, wie die Azure Cosmos DB-Tabellen-API und Azure Storage-Tabellen durch Verwendung des gleichen Datenmodells und der gleichen Vorgänge zusammenarbeiten.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 05/21/2020
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: 5e586ae8a6b6a4010419254ce3e380f377d370f2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101088"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Entwickeln mit der Azure Cosmos DB-Tabellen-API und Azure Table Storage
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Die Azure Cosmos DB-Tabellen-API und Azure Table Storage nutzen dasselbe Tabellendatenmodell und dieselben Erstellungs-, Lösch-, Update- und Abfragevorgänge über ihre SDKs.

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Entwickeln mit der Azure Cosmos DB-Tabellen-API

Derzeit sind für die [Tabellen-API von Azure Cosmos DB](table-introduction.md) vier SDKs zur Entwicklung verfügbar: 

* [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): .NET-SDK. Diese Bibliothek ist für .NET Standard vorgesehen und enthält dieselben Klassen- und Methodensignaturen wie das öffentliche [Microsoft Azure Storage-SDK](https://www.nuget.org/packages/WindowsAzure.Storage), kann aber mithilfe der Tabellen-API auch eine Verbindung mit Azure Cosmos DB-Konten herstellen. Benutzern der .NET Framework-Klassenbibliothek [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) wird empfohlen, ein Upgrade auf [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) durchzuführen, da sie sich im Wartungsmodus befindet und demnächst eingestellt wird.

* [Python SDK](table-sdk-python.md): Das neue Azure Cosmos DB Python SDK ist das einzige SDK, das Azure Table Storage in Python unterstützt. Dieses SDK stellt eine Verbindung mit Azure Table Storage sowie mit der Tabellen-API von Azure Cosmos DB her.

* [Java-SDK](table-sdk-java.md): Dieses Azure Storage SDK kann mithilfe der Tabellen-API eine Verbindung mit Azure Cosmos DB-Konten herstellen.

* [Node.js-SDK](table-sdk-nodejs.md): Dieses Azure Storage SDK kann mithilfe der Tabellen-API eine Verbindung mit Azure Cosmos DB-Konten herstellen.


Weitere Informationen zum Arbeiten mit der Tabellen-API finden Sie im FAQ-Artikel unter [ Tabellen-API](table-api-faq.md).

## <a name="developing-with-azure-table-storage"></a>Entwickeln mit Azure Table Storage

In Azure Table Storage stehen die folgenden SDKs für die Entwicklung zur Verfügung:

- Die Bibliotheken [Microsoft.Azure.Storage.Blob](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/), [Microsoft.Azure.Storage.File](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/), [Microsoft.Azure.Storage.Queue](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/) und [Microsoft.Azure.Storage.Common](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) ermöglichen Ihnen das Arbeiten mit dem Azure Table Storage-Dienst. Wenn Sie die Tabellen-API in Azure Cosmos DB verwenden, können Sie stattdessen die Bibliothek [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) nutzen.
- [Python SDK](https://github.com/Azure/azure-cosmos-table-python). Das Azure Cosmos DB Table SDK für Python unterstützt den Table Storage-Dienst. (Da Azure Table Storage und die Cosmos DB-Tabellen-API die gleichen Features und Funktionen bieten und wir versuchen, unsere SDK-Entwicklung zu faktorisieren, empfehlen wir die Verwendung dieses SDK.)
- [Azure Storage-SDK für Java](https://github.com/azure/azure-storage-java). Dieses Azure Storage SDK enthält eine Clientbibliothek in Java für die Nutzung von Azure Table Storage.
- [Node.js SDK](https://github.com/Azure/azure-storage-node). Dieses SDK bietet ein Node.js-Paket und eine browserkompatible JavaScript-Clientbibliothek für die Nutzung des Table Storage-Diensts.
- [AzureRmStorageTable-PowerShell-Modul](https://www.powershellgallery.com/packages/AzureRmStorageTable). Dieses PowerShell-Modul enthält Cmdlets für die Verwendung mit Speichertabellen.
- [Azure Storage-Clientbibliothek für C++](https://github.com/Azure/azure-storage-cpp/). Diese Clientbibliothek ermöglicht Ihnen die Erstellung von Anwendungen für Azure Storage.
- [Azure Table Storage-Clientbibliothek für Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Dieses Projekt stellt ein Ruby-Paket bereit, das den Zugriff auf Azure Table Storage-Dienste erleichtert.
- [Azure Table Storage-Clientbibliothek für PHP](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Dieses Projekt stellt eine PHP-Clientbibliothek bereit, die den Zugriff auf Azure Table Storage-Dienste erleichtert.


   





