---
title: Azure Cosmos DB-Tabellen-API – .NET SDK und Ressourcen
description: Wichtige Informationen zur Azure Cosmos DB-Tabellen-API einschließlich Veröffentlichungsterminen, Deaktivierungsterminen und Änderungen in den einzelnen Versionen.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.openlocfilehash: db7cc556525ab57f14984232bf1797764865fca3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65606257"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>.NET-API für Azure Cosmos DB-Tabellen: Download und Versionshinweise

> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK-Download**|[NuGet](https://aka.ms/acdbtablenuget)|
|**API-Dokumentation**|[.NET API-Referenzdokumentation](https://aka.ms/acdbtableapiref)|
|**Schnellstart**|[Azure Cosmos DB: Erstellen einer App mit .NET und der Tabellen-API](create-table-dotnet.md)|
|**Tutorial**|[Azure Cosmos DB: Entwickeln mit der Tabellen-API in .NET](tutorial-develop-table-dotnet.md)|
|**Aktuelles unterstütztes Framework**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> Das .NET Framework SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) befindet sich im Wartungsmodus und wird demnächst eingestellt. Aktualisieren Sie auf die neue .NET Standard-Bibliothek [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table), um weiterhin die neuesten Features zu erhalten, die von der Tabellen-API unterstützt werden.

> Wenn Sie während der Vorschauphase ein Tabellen-API-Konto erstellt haben, erstellen Sie ein [neues Tabellen-API-Konto](create-table-dotnet.md#create-a-database-account) für die Verwendung mit den allgemein verfügbaren Table API SDKs.
>

## <a name="release-notes"></a>Versionshinweise

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* Fehlerbehebungen

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* Unterstützung für Schreibvorgänge in mehreren Regionen hinzugefügt.
* NuGet-Paketabhängigkeiten für Microsoft.Azure.DocumentDB, Microsoft.OData.Core, Microsoft.OData.Edm, Microsoft.Spatial korrigiert.

### <a name="a-name113113"></a><a name="1.1.3"/>1.1.3

* NuGet-Paketabhängigkeiten für Microsoft.Azure.Storage.Common und Microsoft.Azure.DocumentDB korrigiert.
* Fehlerkorrekturen bei der Tabellenserialisierung, wenn JsonConvert.DefaultSettings konfiguriert ist.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Überprüfung falsch formatierter ETAGs im Direktmodus wurde hinzugefügt.
* LINQ-Abfragefehler im Gateway-Modus wurde behoben.
* Synchrone APIs werden jetzt im Threadpool mit SynchronizationContext ausgeführt.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Hinzufügen von TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism und TableQueryContinuationTokenLimitInKb zu TableRequestOptions
* Fehlerbehebungen

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Release zur allgemeinen Verfügbarkeit

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0-preview

* Erste Vorschauversion

## <a name="release-and-retirement-dates"></a>Veröffentlichungs- und Deaktivierungstermine

Wenn Microsoft ein SDK deaktiviert, werden Sie mindestens **12 Monate** vorher benachrichtigt, um einen reibungslosen Übergang zu einer neueren/unterstützten Version zu gewährleisten.

Die Bibliothek `Microsoft.Azure.CosmosDB.Table` ist derzeit nur für .NET Framework verfügbar. Sie befindet sich im Wartungsmodus und wird demnächst eingestellt. Neue Features, Funktionen und Optimierungen werden nur der .NET Standard-Bibliothek [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) hinzugefügt. Daher wird ein Upgrade auf [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) empfohlen.

Das Vorschaupaket [WindowsAzure.Storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) ist veraltet. Das SDK WindowsAzure.Storage-PremiumTable wird am 15. November 2018 eingestellt. Ab diesem Zeitpunkt werden Anforderungen an das eingestellte SDK nicht mehr zugelassen. 

Anforderungen an Azure Cosmos DB mithilfe eines deaktivierten SDK werden vom Dienst abgelehnt.
<br/>

| Version | Herausgabedatum | Deaktivierungstermine |
| --- | --- | --- |
| [2.1.0](#2.1.0) |22\. Januar 2019|01\. April 2020 |
| [2.0.0](#2.0.0) |26\. September 2018|01\. März 2020 |
| [1.1.3](#1.1.3) |17\. Juli 2018|01\. Dezember 2019 |
| [1.1.1](#1.1.1) |26\. März 2018|01\. Dezember 2019 |
| [1.1.0](#1.1.0) |21\. Februar 2018|01\. Dezember 2019 |
| [1.0.0](#1.0.0) |15\. November 2017|15\. November 2019 |
| 0.9.0-preview |11\. November 2017 |11\. November 2019 |

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie die folgende Fehlermeldung erhalten: 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

und der Versuch, das Microsoft.Azure.CosmosDB.Table NuGet-Paket zu verwenden zugrunde liegt, haben Sie zwei Möglichkeiten, um das Problem zu beheben:

* Verwenden Sie die Paket-Manager-Konsole, um das Microsoft.Azure.CosmosDB.Table-Paket und seine Abhängigkeiten zu installieren. Geben Sie dazu Folgendes in die Paket-Manager-Konsole für Ihre Projektmappe ein. 

    ```powershell
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```

    
* Installieren Sie das NuGet-Paket „Microsoft.Azure.Storage.Common“ mit Ihrem bevorzugten NuGet-Paketverwaltungstool vor der Installation von „Microsoft.Azure.CosmosDB.Table“.

## <a name="faq"></a>Häufig gestellte Fragen

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Weitere Informationen

Weitere Informationen zur Table-API von Azure Cosmos DB finden Sie in der [Einführung in die Tabellen-API von Azure Cosmos DB](table-introduction.md). 