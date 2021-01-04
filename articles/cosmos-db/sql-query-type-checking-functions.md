---
title: Typüberprüfungsfunktionen in der Abfragesprache für Azure Cosmos DB
description: Erfahren Sie mehr über SQL-Systemfunktionen zur Typüberprüfung in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2becc9216d847dfe26d8fd3a433993112fff7980
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546351"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Typüberprüfungsfunktionen (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Mit den Funktionen für die Typprüfung können Sie den Typ eines Ausdrucks in einer SQL-Abfrage prüfen. Anhand von Typprüfungsfunktionen können Sie während der Ausführung die Typen von Eigenschaften innerhalb von Elementen ermitteln, wenn diese variabel oder unbekannt sind. 

## <a name="functions"></a>Functions

Es folgt eine Tabelle der unterstützten integrierten Typprüfungsfunktionen:

Die folgenden Funktionen unterstützen die Typüberprüfung für Eingabewerte und geben jeweils einen booleschen Wert zurück.  

* [IS_ARRAY](sql-query-is-array.md)
* [IS_BOOL](sql-query-is-bool.md)
* [IS_DEFINED](sql-query-is-defined.md)
* [IS_NULL](sql-query-is-null.md)
* [IS_NUMBER](sql-query-is-number.md)
* [IS_OBJECT](sql-query-is-object.md)
* [IS_PRIMITIVE](sql-query-is-primitive.md)
* [IS_STRING](sql-query-is-string.md)

## <a name="next-steps"></a>Nächste Schritte

- [Systemfunktionen in Azure Cosmos DB](sql-query-system-functions.md)
- [Einführung in Azure Cosmos DB](introduction.md)
- [Benutzerdefinierte Funktionen](sql-query-udfs.md)
- [Aggregate](sql-query-aggregate-functions.md)
