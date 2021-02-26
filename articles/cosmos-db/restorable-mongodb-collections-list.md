---
title: Auflisten wiederherstellbarer Sammlungen in der Azure Cosmos DB-API für MongoDB – REST-API
description: Zeigen Sie den Ereignisfeed aller Mutationen an, die für alle Sammlungen der Azure Cosmos DB-API für MongoDB in einer bestimmten Datenbank erfolgt sind. Dies ist in einem Szenario hilfreich, in dem der Container versehentlich gelöscht wurde.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: c19de134f40c58a687dcf6bac6ac156e46cef7da
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537542"
---
# <a name="list-restorable-collections-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Auflisten wiederherstellbarer Sammlungen in der Azure Cosmos DB-API für MongoDB mithilfe der REST-API

> [!IMPORTANT]
> Die Funktion der Zeitpunktwiederherstellung (fortlaufender Sicherungsmodus) für Azure Cosmos DB befindet sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Zeigen Sie den Ereignisfeed aller Mutationen an, die für alle Sammlungen der Azure Cosmos DB-API für MongoDB in einer bestimmten Datenbank erfolgt sind. Dies ist in einem Szenario hilfreich, in dem der Container versehentlich gelöscht wurde. Diese API erfordert die `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`-Berechtigung.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview
```

Mit optionalen Parametern:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid={restorableMongodbDatabaseRid}
```

## <a name="uri-parameters"></a>URI-Parameter

| Name | Geben Sie in | Erforderlich | type | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |Zeichenfolge| Die instanceId-GUID eines wiederherstellbaren Datenbankkontos. |
| **location** | path | True | Zeichenfolge| Azure Cosmos DB-Region mit Leerzeichen zwischen den Wörtern und Großschreibung der einzelnen Wörter. |
| **subscriptionId** | path | True | Zeichenfolge| Die ID des Zielabonnements. |
| **api-version** | Abfrage | True | Zeichenfolge | Die für diesen Vorgang zu verwendende API-Version. |
| **restorableMongodbDatabaseRid** | Abfrage | |Zeichenfolge| Die Ressourcen-ID der Azure Cosmos DB-API für MongoDB-Datenbank. |

## <a name="responses"></a>Antworten

| Name | type | BESCHREIBUNG |
| --- | --- | --- |
| 200 – OK | [RestorableMongodbCollectionsListResult](#restorablemongodbcollectionslistresult)| Der Vorgang wurde erfolgreich abgeschlossen. |
| Andere Statuscodes | [DefaultErrorResponse](#defaulterrorresponse)| Fehlerantwort mit Beschreibung des Grunds für den Fehler.|

## <a name="examples"></a>Beispiele

### <a name="cosmosdbrestorablemongodbcollectionlist"></a>CosmosDBRestorableMongodbCollectionList

**Beispielanforderung**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid=PD5DALigDgw=
```

**Beispiel für eine Antwort**

Statuscode: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbCollections",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Collection1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definitionen

|Definition  | BESCHREIBUNG|
| --- | --- |
| [DefaultErrorResponse](#defaulterrorresponse) | Eine Fehlerantwort vom Dienst. |
| [ErrorResponse](#errorresponse) | Fehlerantwort. |
| [OperationType](#operationtype) | Enumeration, die den Vorgangstyp des Ereignisses angibt. |
| [Ressource](#resource) | Die Ressource eines Sammlungsereignisses der Azure Cosmos DB-API für MongoDB |
| [RestorableMongodbCollectionGetResult](#restorablemongodbcollectiongetresult) | Ein Sammlungsereignis der Azure Cosmos DB-API für MongoDB |
| [RestorableMongodbCollectionProperties](#restorablemongodbcollectionproperties) | Die Eigenschaften eines Sammlungsereignisses der Azure Cosmos DB-API für MongoDB |
| [RestorableMongodbCollectionsListResult](#restorablemongodbcollectionslistresult) | Die Antwort des Auflistungsvorgangs, die die SQL-Sammlungsereignisse und deren Eigenschaften enthält. |

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
| Erstellen |Zeichenfolge|Sammlungserstellungsereignis|
| Löschen |Zeichenfolge|Sammlungslöschungsereignis|
| Replace |Zeichenfolge|Sammlungsänderungsereignis|

### <a name="resource"></a><a id="resource"></a>Resource

Die Ressource eines Azure Cosmos DB MongoDB-Sammlungsereignisses

| **Name** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| _rid |Zeichenfolge| Eine vom System generierte Eigenschaft. Einen eindeutigen Bezeichner. |
| eventTimestamp |Zeichenfolge| Der Zeitpunkt, zu dem dieses Sammlungsereignis aufgetreten ist. |
| operationType |[OperationType](#operationtype)|  Der Vorgangstyp dieses Sammlungsereignisses. |
| ownerId |Zeichenfolge| Der Name der MongoDB-Sammlung.|
| ownerResourceId |Zeichenfolge| Die Ressourcen-ID der MongoDB-Sammlung. |

### <a name="restorablemongodbcollectiongetresult"></a><a id="restorablemongodbcollectiongetresult"></a>RestorableMongodbCollectionGetResult

Ein Azure Cosmos DB MongoDB-Sammlungsereignis

| **Name** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| id |Zeichenfolge| Der eindeutige Ressourcenbezeichner der Azure Resource Manager-Ressource. |
| name |Zeichenfolge| Der Name der Resource Manager-Ressource. |
| properties |[RestorableMongodbCollectionProperties](#restorablemongodbcollectionproperties)| Die Eigenschaften eines Sammlungsereignisses. |
| type |Zeichenfolge| Der Typ der Azure-Ressource. |

### <a name="restorablemongodbcollectionproperties"></a><a id="restorablemongodbcollectionproperties"></a>RestorableMongodbCollectionProperties

Die Eigenschaften eines Azure Cosmos DB MongoDB-Sammlungsereignisses

| **Name** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| resource | [Ressource](#resource)| Die Ressource eines Sammlungsereignisses der Azure Cosmos DB-API für MongoDB |

### <a name="restorablemongodbcollectionslistresult"></a><a id="restorablemongodbcollectionslistresult"></a>RestorableMongodbCollectionsListResult

Die Antwort des Auflistungsvorgangs, die die SQL-Sammlungsereignisse und deren Eigenschaften enthält.

| **Name** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| value |[RestorableMongodbCollectionGetResult](#restorablemongodbcollectiongetresult)[]| Liste der Sammlungsereignisse der Azure Cosmos DB-API für MongoDB und der zugehörigen Eigenschaften |

## <a name="next-steps"></a>Nächste Schritte

* [Auflisten wiederherstellbarer Datenbanken](restorable-mongodb-databases-list.md) in der Azure Cosmos DB-API für MongoDB mithilfe der REST-API
* [Ressourcenmodell](continuous-backup-restore-resource-model.md) des fortlaufenden Sicherungsmodus