---
title: Auflisten wiederherstellbarer SQL-API-Container in Azure Cosmos DB mithilfe der REST-API
description: Zeigen Sie den Ereignisfeed aller Mutationen an, die für alle Azure Cosmos DB-SQL-Container in einer bestimmten Datenbank erfolgt sind. Dies ist in einem Szenario hilfreich, in dem der Container versehentlich gelöscht wurde.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: bba00b10d1a24dc29c1e986de1f0144c00350f5d
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537423"
---
# <a name="list-restorable-sql-api-containers-in-azure-cosmos-db-using-rest-api"></a>Auflisten wiederherstellbarer SQL-API-Container in Azure Cosmos DB mithilfe der REST-API

> [!IMPORTANT]
> Die Funktion der Zeitpunktwiederherstellung (fortlaufender Sicherungsmodus) für Azure Cosmos DB befindet sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Zeigen Sie den Ereignisfeed aller Mutationen an, die für alle Azure Cosmos DB-SQL-Container in einer bestimmten Datenbank erfolgt sind. Dies ist in einem Szenario hilfreich, in dem der Container versehentlich gelöscht wurde. Diese API erfordert die `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`-Berechtigung.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview
```

Mit optionalen Parametern:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid={restorableSqlDatabaseRid}
```

## <a name="uri-parameters"></a>URI-Parameter

| Name | Geben Sie in | Erforderlich | type | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |Zeichenfolge| Die instanceId-GUID eines wiederherstellbaren Datenbankkontos. |
| **location** | path | True | Zeichenfolge| Azure Cosmos DB-Region mit Leerzeichen zwischen den Wörtern und Großschreibung der einzelnen Wörter. |
| **subscriptionId** | path | True | Zeichenfolge| Die ID des Zielabonnements. |
| **api-version** | Abfrage | True | Zeichenfolge | Die für diesen Vorgang zu verwendende API-Version. |
| **restorableSqlDatabaseRid** | Abfrage | |Zeichenfolge| Die Ressourcen-ID der SQL-Datenbank. |

## <a name="responses"></a>Antworten

| Name | type | BESCHREIBUNG |
| --- | --- | --- |
| 200 – OK | [RestorableSqlContainersListResult](#restorablesqlcontainerslistresult)| Der Vorgang wurde erfolgreich abgeschlossen. |
| Andere Statuscodes | [DefaultErrorResponse](#defaulterrorresponse)| Fehlerantwort mit Beschreibung des Grunds für den Fehler. |

## <a name="examples"></a>Beispiele

### <a name="cosmosdbrestorablesqlcontainerlist"></a>CosmosDBRestorableSqlContainerList

**Beispielanforderung**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid=3fu-hg==
```

**Beispiel für eine Antwort**

Statuscode: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00003e00-0000-0700-0000-5f85338a0000\""
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/e85298a1-c631-4726-825e-a7ca092e9098",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "e85298a1-c631-4726-825e-a7ca092e9098",
      "properties": {
        "resource": {
          "_rid": "PrArcgAAAA==",
          "eventTimestamp": "2020-10-13T05:03:27Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Replace",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "defaultTtl": 12345,
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00004400-0000-0700-0000-5f85351f0000\""
          }
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definitionen

|Definition | Beschreibung| | --- || --- | | [Container](#container) | Azure Cosmos DB-SQL-Containerressourcenobjekt | | [DefaultErrorResponse](#defaulterrorresponse) | Eine Fehlerantwort vom Dienst. | | [ErrorResponse](#errorresponse) | Fehlerantwort. | | [OperationType](#operationtype) | Enumeration, die den Vorgangstyp des Ereignisses angibt. | | [Ressource](#resource) | Die Ressource eines Azure Cosmos DB-SQL-Containerereignisses | | [RestorableSqlContainerGetResult](#restorablesqlcontainergetresult) | Ein Azure Cosmos DB-SQL-Containerereignis | | [RestorableSqlContainerProperties](#restorablesqlcontainerproperties) | Die Eigenschaften eines Azure Cosmos DB-SQL-Containerereignisses | | [RestorableSqlContainersListResult](#restorablesqlcontainerslistresult) | Die Antwort des Auflistungsvorgangs, die die SQL-Containerereignisse und deren Eigenschaften enthält. |

### <a name="container"></a><a id="container"></a>Container

Azure Cosmos DB-SQL-Containerressourcenobjekt

| **Name** | **Typ** | **Beschreibung** | | --- || --- | ---| | _etag |Zeichenfolge| Eine vom System generierte Eigenschaft, die das „etag“ der Ressource darstellt, das für die Steuerung der optimistischen Nebenläufigkeit erforderlich ist. | | _rid |Zeichenfolge| Eine vom System generierte Eigenschaft. Einen eindeutigen Bezeichner. | | _self |Zeichenfolge| Eine vom System generierte Eigenschaft, die den adressierbaren Pfad der Containerressource angibt. | | _ts | | Eine vom System generierte Eigenschaft, die den zuletzt aktualisierten Zeitstempel der Ressource angibt. | | ID |Zeichenfolge| Name des Azure Cosmos DB-SQL-Containers |

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
| Erstellen |Zeichenfolge|Containererstellungsereignis|
| Löschen |Zeichenfolge|Containerlöschungsereignis|
| Replace |Zeichenfolge|Containeränderungsereignis|
| SystemOperation |Zeichenfolge|Ein vom System ausgelöstes Containeränderungsereignis. Dieses Ereignis wird nicht vom Benutzer initiiert.|

### <a name="resource"></a><a id="resource"></a>Resource

Die Ressource eines Azure Cosmos DB-SQL-Containerereignisses

| **Name** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| _rid |Zeichenfolge| Eine vom System generierte Eigenschaft. Einen eindeutigen Bezeichner. |
| Container |[Container](#container)| Azure Cosmos DB-SQL-Containerressourcenobjekt |
| eventTimestamp |Zeichenfolge| Der Zeitpunkt, zu dem dieses Containerereignis aufgetreten ist. |
| operationType |[OperationType](#operationtype)| Der Vorgangstyp dieses Containerereignisses. |
| ownerId |Zeichenfolge| Der Name des SQL-Containers. |
| ownerResourceId |Zeichenfolge| Die Ressourcen-ID des SQL-Containers. |

### <a name="restorablesqlcontainergetresult"></a><a id="restorablesqlcontainergetresult"></a>RestorableSqlContainerGetResult

Ein Azure Cosmos DB-SQL-Containerereignis

| **Name** | **Typ** | **Beschreibung** |
| --- | --- | ---
| id |Zeichenfolge| Der eindeutige Ressourcenbezeichner der Azure Resource Manager-Ressource. |
| name |Zeichenfolge| Der Name der Azure Resource Manager-Ressource. |
| properties |[RestorableSqlContainerProperties](#restorablesqlcontainerproperties)| Die Eigenschaften eines SQL-Containerereignisses. |
| type |Zeichenfolge| Der Typ der Azure-Ressource. |

### <a name="restorablesqlcontainerproperties"></a><a id="restorablesqlcontainerproperties"></a>RestorableSqlContainerProperties

Die Eigenschaften eines Azure Cosmos DB-SQL-Containerereignisses

| **Name** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| resource |[Ressource](#resource)| Die Ressource eines Azure Cosmos DB-SQL-Containerereignisses |

### <a name="restorablesqlcontainerslistresult"></a><a id="restorablesqlcontainerslistresult"></a>RestorableSqlContainersListResult

Die Antwort des Auflistungsvorgangs, die die SQL-Containerereignisse und deren Eigenschaften enthält.

| **Name** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| value |[RestorableSqlContainerGetResult](#restorablesqlcontainergetresult)[]| Liste der SQL-Containerereignisse und deren Eigenschaften. |

## <a name="next-steps"></a>Nächste Schritte

* [Auflisten wiederherstellbarer Datenbanken](restorable-mongodb-databases-list.md) in der Azure Cosmos DB-API für MongoDB mithilfe der REST-API
* [Ressourcenmodell](continuous-backup-restore-resource-model.md) des fortlaufenden Sicherungsmodus