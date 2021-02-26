---
title: Auflisten wiederherstellbarer Datenbankkonten mithilfe der Azure Cosmos DB-REST-API
description: Listet alle wiederherstellbaren Azure Cosmos DB-Datenbankkonten auf, die unter einem Abonnement verfügbar sind.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 71aafc756e5291e148c3b162f8946544b6e3c2d0
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537576"
---
# <a name="list-restorable-database-accounts-using-azure-cosmos-db-rest-api"></a>Auflisten wiederherstellbarer Datenbankkonten mithilfe der Azure Cosmos DB-REST-API

> [!IMPORTANT]
> Die Funktion der Zeitpunktwiederherstellung (fortlaufender Sicherungsmodus) für Azure Cosmos DB befindet sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Listet alle wiederherstellbaren Azure Cosmos DB-Datenbankkonten auf, die unter dem Abonnement verfügbar sind. Für diesen Aufruf ist die Berechtigung `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read` erforderlich.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/restorableDatabaseAccounts?api-version=2020-06-01-preview
```

## <a name="uri-parameters"></a>URI-Parameter

| Name | Geben Sie in | Erforderlich | type | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| **subscriptionId** | path | True | Zeichenfolge| Die ID des Zielabonnements. |
| **api-version** | Abfrage | True | Zeichenfolge | Die für diesen Vorgang zu verwendende API-Version. |

## <a name="responses"></a>Antworten

| Name | type | BESCHREIBUNG |
| --- | --- | --- |
| 200 – OK | [RestorableDatabaseAccountsListResult](#restorabledatabaseaccountslistresult)| Der Vorgang wurde erfolgreich abgeschlossen. |
| Andere Statuscodes | [DefaultErrorResponse](#defaulterrorresponse)| Fehlerantwort mit Beschreibung des Grunds für den Fehler. |

## <a name="examples"></a>Beispiele

### <a name="cosmosdbdatabaseaccountlist"></a>CosmosDBDatabaseAccountList

**Beispielanforderung**

```http
GET https://management.azure.com/subscriptions/subid/providers/Microsoft.DocumentDB/restorableDatabaseAccounts?api-version=2020-06-01-preview
```

**Beispiel für eine Antwort**

Statuscode: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "name": "d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "location": "West US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb1",
        "creationTime": "2020-04-11T21:56:15Z",
        "deletionTime": "2020-06-12T22:05:09Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "name": "4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "location": "East US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb2",
        "creationTime": "2020-05-01T08:05:18Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    }
  ]
}
```

## <a name="definitions"></a>Definitionen

|Definition  | BESCHREIBUNG|
| --- | --- |
| [ApiType](#apitype) | Enumeration, die den API-Typ des wiederherstellbaren Datenbankkontos angibt. |
| [DefaultErrorResponse](#defaulterrorresponse) | Eine Fehlerantwort vom Dienst. |
| [ErrorResponse](#errorresponse) | Fehlerantwort. |
| [RestorableDatabaseAccountGetResult](#restorabledatabaseaccountgetresult) | Ein wiederherstellbares Azure Cosmos DB-Datenbankkonto. |
| [RestorableDatabaseAccountsListResult](#restorabledatabaseaccountslistresult) | Die Antwort des Auflistungsvorgangs, die die wiederherstellbaren Datenbankkonten und deren Eigenschaften enthält. |
| [RestorableLocationResource](#restorablelocationresource) | Die Eigenschaften des regionalen wiederherstellbaren Kontos. |

### <a name="apitype"></a><a id="apitype"></a>ApiType

Enumeration, die den API-Typ des wiederherstellbaren Datenbankkontos angibt.

| **Name** | **Typ** |
| --- | --- |
| Cassandra |Zeichenfolge|
| Gremlin |Zeichenfolge|
| GremlinV2 |Zeichenfolge|
| MongoDB |Zeichenfolge|
| Sql |Zeichenfolge|
| Tabelle |Zeichenfolge|

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

### <a name="restorabledatabaseaccountgetresult"></a><a id="restorabledatabaseaccountgetresult"></a>RestorableDatabaseAccountGetResult

Ein wiederherstellbares Azure Cosmos DB-Datenbankkonto.

| **Name** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| id |Zeichenfolge| Der eindeutige Ressourcenbezeichner der Azure Resource Manager-Ressource. |
| location |Zeichenfolge| Der Speicherort der Ressourcengruppe, zu der die Ressource gehört. |
| name |Zeichenfolge| Der Name der Resource Manager-Ressource. |
| properties.accountName |Zeichenfolge| Der Name des globalen Datenbankkontos |
| properties.apiType |[ApiType](#apitype)| Der API-Typ des wiederherstellbaren Datenbankkontos. |
| properties.creationTime |Zeichenfolge| Die Erstellungszeit des wiederherstellbaren Datenbankkontos (ISO-8601-Format). |
| properties.deletionTime |Zeichenfolge| Der Zeitpunkt, zu dem das wiederherstellbare Datenbankkonto gelöscht wurde (ISO-8601-Format). |
| properties.restorableLocations |[RestorableLocationResource](#restorablelocationresource)[]| Die Liste der Regionen, aus denen das Datenbankkonto wiederhergestellt werden kann. |
| type |Zeichenfolge| Der Typ der Azure-Ressource. |

### <a name="restorabledatabaseaccountslistresult"></a><a id="restorabledatabaseaccountslistresult"></a>RestorableDatabaseAccountsListResult

Die Antwort des Auflistungsvorgangs, die die wiederherstellbaren Datenbankkonten und deren Eigenschaften enthält.

| **Name** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| value |[RestorableDatabaseAccountGetResult](#restorabledatabaseaccountgetresult)[]| Die Liste der wiederherstellbaren Datenbankkonten und deren Eigenschaften. |

### <a name="restorablelocationresource"></a><a id="restorablelocationresource"></a>RestorableLocationResource

Die Eigenschaften des regionalen wiederherstellbaren Kontos.

| **Name** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| creationTime |Zeichenfolge| Die Erstellungszeit des regionalen wiederherstellbaren Datenbankkontos (ISO-8601-Format). |
| deletionTime |Zeichenfolge| Der Zeitpunkt, zu dem das regionale wiederherstellbare Datenbankkonto gelöscht wurde (ISO-8601-Format). |
| locationName |Zeichenfolge| Der Speicherort des regionalen wiederherstellbaren Kontos. |
| regionalDatabaseAccountInstanceId |Zeichenfolge| Die Instanz-ID des regionalen wiederherstellbaren Kontos. |

## <a name="next-steps"></a>Nächste Schritte

* [Wiederherstellbare Datenbankkonten: Auflisten nach Standort](restorable-database-accounts-list-by-location.md)
* [Ressourcenmodell](continuous-backup-restore-resource-model.md) des fortlaufenden Sicherungsmodus