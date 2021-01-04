---
title: 'Azure Resource Manager-Vorlagen für Azure Cosmos DB: Core-API (SQL)'
description: Verwenden Sie Azure Resource Manager-Vorlagen zum Erstellen und Konfigurieren von Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/14/2020
ms.author: mjbrown
ms.openlocfilehash: 52fa5957cdbc3ae73ebc4b36e04582741199414a
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340599"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Resource Manager-Vorlagen für Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Dieser Artikel enthält nur Azure Resource Manager-Vorlagenbeispiele für Core-API-Konten (SQL). Es gibt jedoch auch Vorlagenbeispiele für [Cassandra-](templates-samples-cassandra.md)-, [Gremlin](templates-samples-gremlin.md)-, [MongoDB](templates-samples-mongodb.md)- und [Table](templates-samples-table.md)-APIs.

## <a name="core-sql-api"></a>Core-API (SQL)

|**Vorlage**|**Beschreibung**|
|---|---|
|[Erstellen eines Azure Cosmos-Kontos, einer Datenbank und eines Containers mit automatisch skaliertem Durchsatz](manage-with-templates.md#create-autoscale) | Mit dieser Vorlage werden ein Core-API-Konto (SQL) in zwei Regionen, eine Datenbank und ein Container mit automatisch skaliertem Durchsatz erstellt. |
|[Erstellen eines Azure Cosmos-Kontos, einer Datenbank und eines Containers mit Analysespeicher](manage-with-templates.md#create-analytical-store) | Mit dieser Vorlage wird ein Konto für die Core-API (SQL) in einer Region mit einem Container erstellt, für den die analytische Gültigkeitsdauer aktiviert ist, und es besteht die Möglichkeit, die manuelle oder automatische Skalierung des Durchsatzes zu verwenden. |
|[Erstellen eines Azure Cosmos-Kontos, einer Datenbank und eines Containers mit (manuell bereitgestelltem) Standarddurchsatz](manage-with-templates.md#create-manual) | Mit dieser Vorlage werden ein Core-API-Konto (SQL) in zwei Regionen, eine Datenbank und ein Container mit Standarddurchsatz erstellt. |
|[Erstellen eines Azure Cosmos-Kontos, einer Datenbank und eines Containers mit einer gespeicherten Prozedur, einem Trigger und einer UDF](manage-with-templates.md#create-sproc) | Mit dieser Vorlage werden ein Core-API-Konto (SQL) in zwei Regionen mit einer gespeicherten Prozedur, einem Trigger und einer UDF für einen Container erstellt. |
|[Erstellen eines privaten Endpunkts für ein vorhandenes Azure Cosmos-Konto](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Mit dieser Vorlage wird ein privater Endpunkt für ein vorhandenes Azure Cosmos-Core-API-Konto (SQL) in einem vorhandenen virtuellen Netzwerk erstellt. |
|[Erstellen eines Azure Cosmos-Kontos im Free-Tarif](manage-with-templates.md#free-tier) |  Mit dieser Vorlage wird ein Azure Cosmos DB-Core-API-Konto (SQL) im Free-Tarif erstellt. |

Auf der Seite [Azure Resource Manager-Referenz für Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) finden Sie die Referenzdokumentation.
