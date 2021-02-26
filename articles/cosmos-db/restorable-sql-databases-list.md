---
title: Auflisten wiederherstellbarer SQL-API-Datenbanken in Azure Cosmos DB mithilfe der REST-API
description: Zeigen Sie den Ereignisfeed aller Mutationen an, die für alle Azure Cosmos DB-SQL-Datenbanken unter dem wiederherstellbaren Konto erfolgt sind. Dies ist in einem Szenario hilfreich, in dem die Datenbank versehentlich gelöscht wurde, um den Löschzeitpunkt zu ermitteln.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 218a39a2bf8e9269e43c4876c1654d94be886997
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539500"
---
# <a name="list-restorable-sql-api-databases-in-azure-cosmos-db-using-rest-api"></a>Auflisten wiederherstellbarer SQL-API-Datenbanken in Azure Cosmos DB mithilfe der REST-API

> [!IMPORTANT]
> Die Funktion der Zeitpunktwiederherstellung (fortlaufender Sicherungsmodus) für Azure Cosmos DB befindet sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Zeigen Sie den Ereignisfeed aller Mutationen an, die für alle Azure Cosmos DB-SQL-Datenbanken unter dem wiederherstellbaren Konto erfolgt sind. Dies ist in einem Szenario hilfreich, in dem die Datenbank versehentlich gelöscht wurde, um den Löschzeitpunkt zu ermitteln. Diese API erfordert die `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`-Berechtigung.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlDatabases?api-version=2020-06-01-preview
```

## <a name="uri-parameters"></a>URI-Parameter

| Name | Geben Sie in | Erforderlich | type | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |Zeichenfolge| Die instanceId-GUID eines wiederherstellbaren Datenbankkontos. |
| **location** | path | True | Zeichenfolge| Azure Cosmos DB-Region mit Leerzeichen zwischen den Wörtern und Großschreibung der einzelnen Wörter. |
| **subscriptionId** | path | True | Zeichenfolge| Die ID des Zielabonnements. |
| **api-version** | Abfrage | True | Zeichenfolge | Die für diesen Vorgang zu verwendende API-Version. |

## <a name="responses"></a>Antworten

| Name | type | BESCHREIBUNG |
| --- | --- | --- |
| 200 – OK | [RestorableSqlDatabasesListResult](#restorablesqldatabaseslistresult)| Der Vorgang wurde erfolgreich abgeschlossen. |
| Andere Statuscodes | [DefaultErrorResponse](#defaulterrorresponse)| Fehlerantwort mit Beschreibung des Grunds für den Fehler. |

## <a name="examples"></a>Beispiele

### <a name="cosmosdbrestorablesqldatabaselist"></a>CosmosDBRestorableSqlDatabaseList

**Beispiel für eine Anforderung**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases?api-version=2020-06-01-preview
```

**Beispiel für eine Antwort**

Statuscode: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableSqlDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "operationType": "Create",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/"
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/",
            "_ts": 1599075903
          },
          "operationType": "Delete"
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "properties": {
        "resource": {
          "_rid": "aXFqUQAAAA==",
          "eventTimestamp": "2020-09-02T19:53:15Z",
          "ownerId": "Database2",
          "ownerResourceId": "0SziSg==",
          "database": {
            "id": "Database2",
            "_rid": "0SziSg==",
            "_self": "dbs/0SziSg==/",
            "_etag": "\"0000ca0a-0000-0700-0000-5f4ff82b0000\"",
            "_colls": "colls/",
            "_users": "users/"
          },
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definitionen

|Definition | Beschreibung| | --- || --- | | [Datenbank](#database) | Azure Cosmos DB-SQL-Datenbankressourcenobjekt | | [DefaultErrorResponse](#defaulterrorresponse) | Eine Fehlerantwort vom Dienst. | | [ErrorResponse](#errorresponse) | Fehlerantwort. | | [OperationType](#operationtype) | Enumeration, die den Vorgangstyp des Ereignisses angibt. | | [Ressource](#resource) | Die Ressource eines Azure Cosmos DB-SQL-Datenbankereignisses | | [RestorableSqlDatabaseGetResult](#restorablesqldatabasegetresult) | Ein Azure Cosmos DB-SQL-Datenbankereignis | | [RestorableSqlDatabaseProperties](#restorablesqldatabaseproperties) | Die Eigenschaften eines Azure Cosmos DB-SQL-Datenbankereignisses | | [RestorableSqlDatabasesListResult](#restorablesqldatabaseslistresult) | Die Antwort des Auflistungsvorgangs, die die SQL-Datenbankereignisse und deren Eigenschaften enthält. |

### <a name="database"></a><a id="database"></a>Datenbank

Azure Cosmos DB-SQL-Datenbankressourcenobjekt

| **Name** | **Typ** | **Beschreibung** | | --- || --- | ---| | _colls |Zeichenfolge| Eine vom System generierte Eigenschaft, die den adressierbaren Pfad der Auflistungsressource angibt. | | _etag |Zeichenfolge| Eine vom System generierte Eigenschaft, die das „etag“ der Ressource darstellt, das für die Steuerung der optimistischen Nebenläufigkeit erforderlich ist. | | _rid |Zeichenfolge| Eine vom System generierte Eigenschaft. Einen eindeutigen Bezeichner. | | _self |Zeichenfolge| Eine vom System generierte Eigenschaft, die den adressierbaren Pfad der Datenbankressource angibt. | | _ts | | Eine vom System generierte Eigenschaft, die den zuletzt aktualisierten Zeitstempel der Ressource angibt. | | _users |Zeichenfolge| Eine vom System generierte Eigenschaft, die den adressierbaren Pfad der Benutzerressource angibt. | | ID |Zeichenfolge| Name der Azure Cosmos DB-SQL-Datenbank |

### <a name="defaulterrorresponse"></a><a id="defaulterrorresponse"></a>DefaultErrorResponse

Eine Fehlerantwort vom Dienst.

| **Name** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| Fehler | [ErrorResponse](#errorresponse)| Fehlerantwort. |

### <a name="errorresponse"></a><a id="errorresponse"></a>ErrorResponse

Fehlerantwort.

| **Name** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| code |Zeichenfolge| Fehlercode |
| message |Zeichenfolge| Eine Fehlermeldung, die angibt, warum der Vorgang fehlgeschlagen ist. |

### <a name="operationtype"></a><a id="operationtype"></a>OperationType

Enumeration, die den Vorgangstyp des Ereignisses angibt.

| **Name** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| Erstellen |Zeichenfolge|Datenbankerstellungsereignis|
| Löschen |Zeichenfolge|Datenbanklöschungsereignis|
| Replace |Zeichenfolge|Datenbankänderungsereignis|
| SystemOperation |Zeichenfolge|Ein vom System ausgelöstes Datenbankänderungsereignis. Dieses Ereignis wird nicht vom Benutzer initiiert.|

### <a name="resource"></a><a id="resource"></a>Resource

Die Ressource eines Azure Cosmos DB-SQL-Datenbankereignisses

| **Name** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| _rid |Zeichenfolge| Eine vom System generierte Eigenschaft. Einen eindeutigen Bezeichner. |
| database |[Datenbank](#database)| Azure Cosmos DB-SQL-Datenbankressourcenobjekt |
| eventTimestamp |Zeichenfolge| Der Zeitpunkt, zu dem dieses Datenbankereignis aufgetreten ist. |
| operationType |[OperationType](#operationtype)| Der Vorgangstyp dieses Datenbankereignisses. |
| ownerId |Zeichenfolge| Der Name der SQL-Datenbank. |
| ownerResourceId |Zeichenfolge| Die Ressourcen-ID der SQL-Datenbank. |

### <a name="restorablesqldatabasegetresult"></a><a id="restorablesqldatabasegetresult"></a>RestorableSqlDatabaseGetResult

Ein Azure Cosmos DB-SQL-Datenbankereignis

| **Name** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| id |Zeichenfolge| Der eindeutige Ressourcenbezeichner der Azure Resource Manager-Ressource. |
| name |Zeichenfolge| Der Name der Azure Resource Manager-Ressource. |
| properties | [RestorableSqlDatabaseProperties](#restorablesqldatabaseproperties)| Die Eigenschaften eines SQL-Datenbankereignisses. |
| type |Zeichenfolge| Der Typ der Azure-Ressource. |

### <a name="restorablesqldatabaseproperties"></a><a id="restorablesqldatabaseproperties"></a>RestorableSqlDatabaseProperties

Die Eigenschaften eines Azure Cosmos DB-SQL-Datenbankereignisses

| **Name** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| resource |[Ressource](#resource)| Die Ressource eines Azure Cosmos DB-SQL-Datenbankereignisses |

### <a name="restorablesqldatabaseslistresult"></a><a id="restorablesqldatabaseslistresult"></a>RestorableSqlDatabasesListResult

Die Antwort des Auflistungsvorgangs, die die SQL-Datenbankereignisse und deren Eigenschaften enthält.

| **Name** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| value |[RestorableSqlDatabaseGetResult](#restorablesqldatabasegetresult)[]| Liste der SQL-Datenbankereignisse und deren Eigenschaften. |

## <a name="next-steps"></a>Nächste Schritte

* [Auflisten wiederherstellbarer SQL-API-Container](restorable-sql-containers-list.md) in Azure Cosmos DB mithilfe der REST-API
* [Ressourcenmodell](continuous-backup-restore-resource-model.md) des fortlaufenden Sicherungsmodus