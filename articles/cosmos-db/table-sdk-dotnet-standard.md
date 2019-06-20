---
title: 'Azure Cosmos DB-Tabellen-API: .NET Standard SDK und Ressourcen'
description: Enthält Informationen zur Azure Cosmos DB-Tabellen-API und zum .NET Standard SDK, z.B. Veröffentlichungstermine, Deaktivierungstermine und Änderungen an den einzelnen Versionen.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: 1c9894772f440a568cbc08890feb961471d84137
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480216"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>.NET Standard-API für Azure Cosmos DB-Tabellen: Download und Versionshinweise
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK-Download**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Beispiel**|[Cosmos DB-Tabellen-API .NET-Beispiel](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Schnellstart**|[Schnellstart](create-table-dotnet.md)|
|**Tutorial**|[Tutorial](tutorial-develop-table-dotnet.md)|
|**Aktuelles unterstütztes Framework**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>Versionshinweise

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Fehlerbehebungen

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Release zur allgemeinen Verfügbarkeit

### <a name="a-name0110-preview0110-preview"></a><a name="0.11.0-preview"/>0.11.0-preview
* Es wurden Änderungen vorgenommen, wie CloudTableClient konfiguriert werden kann. Es akzeptiert jetzt ein TableClientConfiguration-Objekt während der Erstellung. TableClientConfiguration bietet verschiedene Eigenschaften, um das Clientverhalten in Abhängigkeit davon zu konfigurieren, ob der Zielendpunkt eine Cosmos DB-Tabellen-API oder Azure Storage-Tabellen-API ist.
* Unterstützung für TableQuery wurde hinzugefügt, um Ergebnisse in sortierter Reihenfolge für eine benutzerdefinierte Spalte zurückzugeben. Diese Funktion wird nur für Cosmos DB-Tabellenendpunkte unterstützt.
* Unterstützung wurde hinzugefügt, um RequestCharges für verschiedene Ergebnistypen verfügbar zu machen. Diese Funktion wird nur für Cosmos DB-Tabellenendpunkte unterstützt.

### <a name="a-name0101-preview0101-preview"></a><a name="0.10.1-preview"/>0.10.1-preview
* Es wurde Unterstützung für SAS-Token, TablePermissions-, ServiceProperties- und ServiceStats-Vorgänge für Azure Storage-Tabellenendpunkte hinzugefügt. 
   > [!NOTE]
   > Einige Funktionen in vorherigen Azure Storage-Tabellen-SDKs werden noch nicht unterstützt (z.B. die clientseitige Verschlüsselung).

### <a name="a-name0100-preview0100-preview"></a><a name="0.10.0-preview"/>0.10.0-preview
* Es wurde Unterstützung für grundlegende CRUD-, Batch- und Abfragevorgänge für Azure Storage-Tabellenendpunkte hinzugefügt. 
   > [!NOTE]
   > Einige Funktionen in vorherigen Azure Storage-Tabellen-SDKs werden noch nicht unterstützt (z.B. die clientseitige Verschlüsselung).

### <a name="a-name091-preview091-preview"></a><a name="0.9.1-preview"/>0.9.1-preview
* „Azure Cosmos DB-Tabellen-API – .NET Standard SDK“ ist eine plattformübergreifende .NET-Bibliothek, die den effizienten Zugriff auf das Tabellendatenmodell unter Cosmos DB ermöglicht. Diese erste Version unterstützt den gesamten Satz mit CRUD- und Abfragefunktionen für Tabellen- und Entitätsvorgänge mit ähnlichen APIs wie das [Cosmos DB-Tabellen-SDK für .NET Framework](table-sdk-dotnet.md). 
   > [!NOTE]
   >  Azure Storage-Tabellenendpunkte werden in der Version 0.9.1-preview noch nicht unterstützt.

## <a name="release-and-retirement-dates"></a>Veröffentlichungs- und Deaktivierungstermine
Wenn Microsoft ein SDK deaktiviert, werden Sie mindestens **12 Monate** vorher benachrichtigt, um einen reibungslosen Übergang zu einer neueren/unterstützten Version zu gewährleisten.

Die plattformübergreifende .NET Standard-Bibiliothek [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) ersetzt die .NET Framework-Bibliothek [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table).

| Version | Herausgabedatum | Deaktivierungstermine |
| --- | --- | --- |
| [1.0.1](#1.0.1) |19. April 2019 |--- |
| [1.0.0](#1.0.0) |13. März 2019 |--- |
| [0.11.0-preview](#0.11.0-preview) |5. März 2019 |--- |
| [0.10.1-preview](#0.10.1-preview) |22. Januar 2019 |--- |
| [0.10.0-preview](#0.10.0-preview) |18. Dezember 2018 |--- |
| [0.9.1-preview](#0.9.1-preview) |18. Oktober 2018 |--- |


## <a name="faq"></a>Häufig gestellte Fragen

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Weitere Informationen
Weitere Informationen zur Table-API von Azure Cosmos DB finden Sie in der [Einführung in die Tabellen-API von Azure Cosmos DB](table-introduction.md). 
