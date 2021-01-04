---
title: Einrichten einer Verbindung mit einer Datenquelle mithilfe einer verwalteten Identität
titleSuffix: Azure Cognitive Search
description: Erfahren Sie, wie Sie eine Indexerverbindung mit einer Datenquelle mithilfe einer verwalteten Identität einrichten
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 95f1c4bf9b599da8285ac69e299549e5aa73c2f9
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519587"
---
# <a name="set-up-an-indexer-connection-to-a-data-source-using-a-managed-identity"></a>Einrichten einer Indexerverbindung mit einer Datenquelle mithilfe einer verwalteten Identität

> [!IMPORTANT] 
> Das Einrichten einer Verbindung mit einer Datenquelle mithilfe einer verwalteten Identität mit dem Tarif „Free“ von Azure Cognitive Search wird nicht unterstützt.

Ein [Indexer](search-indexer-overview.md) in Azure Cognitive Search ist ein Crawler, mit dem Daten aus Ihrer Datenquelle in Azure Cognitive Search abgerufen werden können. Ein Indexer ruft eine Datenquellenverbindung aus dem von Ihnen erstellten Datenquellenobjekt ab. Das Datenquellenobjekt enthält im Allgemeinen Anmeldeinformationen für die Zieldatenquelle. Beispielsweise kann das Datenquellenobjekt einen Azure Storage-Kontoschlüssel enthalten, wenn Sie Daten aus einem Blobspeichercontainer indizieren möchten.

In vielen Fällen lassen sich Anmeldeinformationen problemlos direkt im Datenquellenobjekt bereitstellen, es können sich jedoch auch Fragen ergeben:
* Wie lassen sich die Anmeldeinformationen in meinem Code schützen, mit dem das Datenquellenobjekt erstellt wird?
* Wenn mein Kontoschlüssel oder Kennwort kompromittiert ist und geändert werden muss, muss ich dann meine Datenquellenobjekte mit dem neuen Kontoschlüssel oder Kennwort aktualisieren, damit der Indexer wieder eine Verbindung mit einer Datenquelle herstellen kann?

Diese Probleme lassen sich durch Einrichten der Verbindung mithilfe einer verwalteten Identität lösen.

## <a name="using-managed-identities"></a>Verwenden von verwalteten Identitäten

[Verwaltete Identitäten](../active-directory/managed-identities-azure-resources/overview.md) sind eine Funktion, die für Azure-Dienste eine automatisch verwaltete Identität in Azure Active Directory (Azure AD) bereitstellt. Sie können diese Funktion in Azure Cognitive Search verwenden, um ein Datenquellenobjekt mit einer Verbindungszeichenfolge zu erstellen, die keine Anmeldeinformationen enthält. Stattdessen wird dem Suchdienst durch die rollenbasierte Zugriffssteuerung in Azure (RBAC) Zugriff auf die Datenquelle erteilt.

Wenn Sie eine Datenquelle mithilfe einer verwalteten Identität einrichten, können Sie die Anmeldeinformationen der Datenquelle ändern, und die Indexer können weiterhin eine Verbindung mit der Datenquelle herstellen. Sie können außerdem Datenquellenobjekte in Ihrem Code erstellen, ohne einen Kontoschlüssel einfügen oder Key Vault zum Abrufen eines Kontoschlüssels verwenden zu müssen.

## <a name="limitations"></a>Einschränkungen

In folgenden Datenquellen können Indexerverbindungen mithilfe verwalteter Identitäten eingerichtet werden. 

* [Azure Blob Storage, Azure Data Lake Storage Gen2 (Vorschau), Azure Table Storage](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL-Datenbank](search-howto-managed-identities-sql.md)

Bei folgenden Funktionen kann die Verbindung derzeit nicht mithilfe verwalteter Identitäten eingerichtet werden:
* Wissensspeicher
* Benutzerdefinierte Qualifikationen
 
## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Einrichten einer Indexerverbindung mithilfe verwalteter Identitäten:

* [Azure Blob Storage, Azure Data Lake Storage Gen2 (Vorschau), Azure Table Storage](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL-Datenbank](search-howto-managed-identities-sql.md)