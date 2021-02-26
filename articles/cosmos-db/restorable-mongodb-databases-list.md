---
title: Auflisten wiederherstellbarer Datenbanken in der Azure Cosmos DB-API für MongoDB mithilfe der REST-API
description: Zeigen Sie den Ereignisfeed aller Mutationen an, die für alle Azure Cosmos DB MongoDB-Datenbanken unter dem wiederherstellbaren Konto erfolgt sind. Dies ist in einem Szenario hilfreich, in dem die Datenbank versehentlich gelöscht wurde, um den Löschzeitpunkt zu ermitteln.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 5ebb0a23822074f61a16bf1d7652dba589399542
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537509"
---
# <a name="list-restorable-databases-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Auflisten wiederherstellbarer Datenbanken in der Azure Cosmos DB-API für MongoDB mithilfe der REST-API

> [!IMPORTANT]
> Die Funktion der Zeitpunktwiederherstellung (fortlaufender Sicherungsmodus) für Azure Cosmos DB befindet sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Zeigen Sie den Ereignisfeed aller Mutationen an, die für alle Azure Cosmos DB MongoDB-Datenbanken unter dem wiederherstellbaren Konto erfolgt sind. Dies ist in einem Szenario hilfreich, in dem die Datenbank versehentlich gelöscht wurde, um den Löschzeitpunkt zu ermitteln. Diese API erfordert die `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`-Berechtigung.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbDatabases?api-version=2020-06-01-preview
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
| 200 – OK | [RestorableMongodbDatabasesListResult](#restorablemongodbdatabaseslistresult)| Der Vorgang wurde erfolgreich abgeschlossen. |
| Andere Statuscodes | [DefaultErrorResponse](#defaulterrorresponse)| Fehlerantwort mit Beschreibung des Grunds für den Fehler.|

## <a name="examples"></a>Beispiele

### <a name="cosmosdbrestorablemongodbdatabaselist"></a>CosmosDBRestorableMongodbDatabaseList

**Beispielanforderung**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases?api-version=2020-06-01-preview
```

**Beispiel für eine Antwort**

Statuscode: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableMongodbDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Create"
        }
      }
    },
    {
      "id": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Delete"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definitionen

|Definition | Beschreibung| | --- || --- | | [DefaultErrorResponse](#defaulterrorresponse) | Eine Fehlerantwort vom Dienst. | | [ErrorResponse](#errorresponse) | Fehlerantwort. | | [OperationType](#operationtype) | Enumeration, die den Vorgangstyp des Ereignisses angibt. | | [Ressource](#resource) | Die Ressource eines Datenbankereignisses (Azure Cosmos DB-API für MongoDB) | | [RestorableMongodbDatabaseGetResult](#restorablemongodbdatabasegetresult) | Ein Datenbankereignis (Azure Cosmos DB-API für MongoDB) | | [RestorableMongodbDatabaseProperties](#restorablemongodbdatabaseproperties) | Die Eigenschaften eines Datenbankereignisses (Azure Cosmos DB-API für MongoDB) | | [RestorableMongodbDatabasesListResult](#restorablemongodbdatabaseslistresult) | Die Antwort des Auflistungsvorgangs, die die Datenbankereignisse (Azure Cosmos DB-API für MongoDB) und deren Eigenschaften enthält. |

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

### <a name="resource"></a><a id="resource"></a>Resource

Die Ressource eines Azure Cosmos DB-API für MongoDB-Datenbankereignisses

| **Name** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| _rid |Zeichenfolge| Eine vom System generierte Eigenschaft. Einen eindeutigen Bezeichner. |
| eventTimestamp |Zeichenfolge| Der Zeitpunkt, zu dem dieses Datenbankereignis aufgetreten ist. |
| operationType |[OperationType](#operationtype)| Der Vorgangstyp dieses Datenbankereignisses.  |
| ownerId |Zeichenfolge| Der Name der Azure Cosmos DB-API für MongoDB-Datenbank.|
| ownerResourceId |Zeichenfolge| Die Ressourcen-ID der Azure Cosmos DB-API für MongoDB-Datenbank. |

### <a name="restorablemongodbdatabasegetresult"></a><a id="restorablemongodbdatabasegetresult"></a>RestorableMongodbDatabaseGetResult

Ein Azure Cosmos DB-API für MongoDB-Datenbankereignis

| **Name** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| id |Zeichenfolge| Der eindeutige Ressourcenbezeichner der Azure Resource Manager-Ressource. |
| name |Zeichenfolge| Der Name der Resource Manager-Ressource. |
| properties |[RestorableMongodbDatabaseProperties](#restorablemongodbdatabaseproperties)| Die Eigenschaften eines Azure Cosmos DB-API für MongoDB-Datenbankereignisses. |
| type |Zeichenfolge| Der Typ der Azure-Ressource. |

### <a name="restorablemongodbdatabaseproperties"></a><a id="restorablemongodbdatabaseproperties"></a>RestorableMongodbDatabaseProperties

Die Eigenschaften eines Azure Cosmos DB-API für MongoDB-Datenbankereignisses

| **Name** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| resource |[Ressource](#resource)| Die Ressource eines Azure Cosmos DB-API für MongoDB-Datenbankereignisses |

### <a name="restorablemongodbdatabaseslistresult"></a><a id="restorablemongodbdatabaseslistresult"></a>RestorableMongodbDatabasesListResult

Die Antwort des Auflistungsvorgangs, die die Datenbankereignisse und deren Eigenschaften enthält.

| **Name** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| value |[RestorableMongodbDatabaseGetResult](#restorablemongodbdatabasegetresult)[]| Liste der Datenbankereignisse und deren Eigenschaften. |

## <a name="next-steps"></a>Nächste Schritte

* [Auflisten wiederherstellbarer Ressourcen](restorable-mongodb-resources-list.md) in der Azure Cosmos DB-API für MongoDB mithilfe der REST-API
* [Auflisten wiederherstellbarer Sammlungen](restorable-mongodb-collections-list.md) in der Azure Cosmos DB-API für MongoDB mithilfe der REST-API
* [Ressourcenmodell](continuous-backup-restore-resource-model.md) des fortlaufenden Sicherungsmodus