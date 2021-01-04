---
title: 'Tutorial: Wie werden Abfragen mit SQL in Azure Cosmos DB durchgeführt?'
description: 'Tutorial: Hier wird beschrieben, wie Sie SQL-Abfragen in Azure Cosmos DB mithilfe des Abfrageplaygrounds durchführen.'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.custom: tutorial-develop, mvc
ms.topic: tutorial
ms.date: 11/05/2019
ms.reviewer: sngun
ms.openlocfilehash: c206b68495b09988e49f37433ccd5616acd29cb7
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93337777"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-sql-api"></a>Tutorial: Abfragen von Azure Cosmos BD mithilfe der SQL-API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Die [SQL-API](./introduction.md) von Azure Cosmos DB unterstützt die Abfrage von Dokumenten mit SQL. Dieser Artikel stellt ein Beispieldokument sowie zwei SQL-Beispielabfragen und Ergebnisse vor.

In diesem Artikel werden die folgenden Aufgaben behandelt: 

> [!div class="checklist"]
> * Abfragen von Daten mit SQL

## <a name="sample-document"></a>Beispieldokument

Die SQL-Abfragen in diesem Artikel verwenden das folgende Beispieldokument.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

## <a name="where-can-i-run-sql-queries"></a>Wo kann ich SQL-Abfragen durchführen?

Sie können Abfragen mit dem Daten-Explorer im Azure-Portal, über die [REST-API und SDKs](sql-api-sdk-dotnet.md) und auch auf dem [Query Playground](https://www.documentdb.com/sql/demo) durchführen, der Abfragen an einem vorhandenen Satz von Beispieldaten durchführt.

Weitere Informationen zu SQL-Abfragen finden Sie hier:
* [SQL-Abfrage und SQL-Syntax](sql-query-getting-started.md)

## <a name="prerequisites"></a>Voraussetzungen

Dieses Tutorial setzt voraus, dass Sie über Azure Cosmos DB-Konto und -Sammlung verfügen. Sie verfügen über keine dieser Ressourcen? Führen Sie den [5-Minuten-Schnellstart](create-cosmosdb-resources-portal.md) aus.

## <a name="example-query-1"></a>Beispielabfrage 1

Im obigen Familienbeispieldokument gibt die folgende SQL-Abfrage die Dokumente zurück, in denen das Feld „ID“ mit `WakefieldFamily` übereinstimmt. Da es sich um eine `SELECT *`-Anweisung handelt, ist die Ausgabe der Abfrage das komplette JSON-Dokument:

**Abfrage**

```sql
    SELECT * 
    FROM Families f 
    WHERE f.id = "WakefieldFamily"
```

**Ergebnisse**

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

## <a name="example-query-2"></a>Beispielabfrage 2

Die nächste Abfrage gibt alle Vornamen von Kindern der Familie zurück, deren ID `WakefieldFamily` entspricht.

**Abfrage**

```sql
    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
```

**Ergebnisse**

```
[
    {
        "givenName": "Jesse"
    },
    {
        "givenName": "Lisa"
    }
]
```


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die folgenden Aufgaben ausgeführt:

> [!div class="checklist"]
> * Sie haben gelernt, wie Sie Abfragen mithilfe von SQL durchführen.  

Sie können jetzt mit dem nächsten Tutorial fortfahren, um zu erfahren, wie Sie Ihre Daten global verteilen.

> [!div class="nextstepaction"]
> [Globales Verteilen Ihrer Daten](tutorial-global-distribution-sql-api.md)