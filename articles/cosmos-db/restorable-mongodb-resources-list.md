---
title: Auflisten wiederherstellbarer Ressourcen in der Azure Cosmos DB-API für MongoDB mithilfe der REST-API
description: Geben Sie eine Liste der Datenbank- und Sammlungskombinationen zurück, die für das Konto zur angegebenen Zeit (Zeitstempel) und am angegebenen Speicherort vorhanden sind. Dies ist in Szenarien hilfreich, in denen überprüft werden soll, welche Ressourcen zu einer bestimmten Zeit (Zeitstempel) und an einem bestimmten Speicherort vorhanden sind.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: b5800c2e9c1f3b36a3ac9afe6cd68f706505fbe0
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537440"
---
# <a name="list-restorable-resources-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Auflisten wiederherstellbarer Ressourcen in der Azure Cosmos DB-API für MongoDB mithilfe der REST-API

> [!IMPORTANT]
> Die Funktion der Zeitpunktwiederherstellung (fortlaufender Sicherungsmodus) für Azure Cosmos DB befindet sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Geben Sie eine Liste der Datenbank- und Sammlungskombinationen zurück, die für das Konto zur angegebenen Zeit (Zeitstempel) und am angegebenen Speicherort vorhanden sind. Dies ist in Szenarien hilfreich, in denen überprüft werden soll, welche Ressourcen zu einer bestimmten Zeit (Zeitstempel) und an einem bestimmten Speicherort vorhanden sind. Diese API erfordert die `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`-Berechtigung.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbResources?api-version=2020-06-01-preview
```

Mit optionalen Parametern:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbResources?api-version=2020-06-01-preview&amp;restoreLocation={restoreLocation}&amp;restoreTimestampInUtc={restoreTimestampInUtc}
```

## <a name="uri-parameters"></a>URI-Parameter

| Name | Geben Sie in | Erforderlich | type | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |Zeichenfolge| Die instanceId-GUID eines wiederherstellbaren Datenbankkontos. |
| **location** | path | True | Zeichenfolge| Azure Cosmos DB-Region mit Leerzeichen zwischen den Wörtern und Großschreibung der einzelnen Wörter. |
| **subscriptionId** | path | True | Zeichenfolge| Die ID des Zielabonnements. |
| **api-version** | Abfrage | True | Zeichenfolge | Die für diesen Vorgang zu verwendende API-Version. |
| **restoreLocation** | Abfrage | |Zeichenfolge| Der Speicherort, an dem sich die wiederherstellbaren Ressourcen befinden. |
| **restoreTimestampInUtc** | Abfrage | |Zeichenfolge| Der Zeitstempel, zu dem die wiederherstellbaren Ressourcen vorhanden waren. |

## <a name="responses"></a>Antworten

| Name | type | BESCHREIBUNG |
| --- | --- | --- |
| 200 – OK | [RestorableMongodbResourcesListResult](#restorablemongodbresourceslistresult)| Der Vorgang wurde erfolgreich abgeschlossen. |
| Andere Statuscodes | [DefaultErrorResponse](#defaulterrorresponse)| Fehlerantwort mit Beschreibung des Grunds für den Fehler. |


## <a name="examples"></a>Beispiele

### <a name="cosmosdbrestorablemongodbresourcelist"></a>CosmosDBRestorableMongodbResourceList

**Beispielanforderung**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbResources?api-version=2020-06-01-preview&amp;restoreLocation=WestUS&amp;restoreTimestampInUtc=10/13/2020 4:56
```

**Beispiel für eine Antwort**

Statuscode: 200

```json
{
  "value": [
    {
      "databaseName": "Database1",
      "collectionNames": [
        "Collection1"
      ]
    },
    {
      "databaseName": "Database2",
      "collectionNames": [
        "Collection1",
        "Collection2"
      ]
    },
    {
      "databaseName": "Database3",
      "collectionNames": []
    }
  ]
}
```

## <a name="definitions"></a>Definitionen

|Definition | Beschreibung| | --- || --- | | [DatabaseRestoreResource](#databaserestoreresource) | Bestimmte wiederherzustellende Datenbanken. | | [DefaultErrorResponse](#defaulterrorresponse) | Eine Fehlerantwort vom Dienst. | | [ErrorResponse](#errorresponse) | Fehlerantwort. | | [RestorableMongodbResourcesListResult](#restorablemongodbresourceslistresult) | Die Antwort des Auflistungsvorgangs, die die wiederherstellbaren SQL-Ressourcen enthält. |

### <a name="databaserestoreresource"></a><a id="databaserestoreresource"></a>DatabaseRestoreResource

Bestimmte wiederherzustellende Datenbanken.

| **Name** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| collectionNames |string[]| Die Namen der für die Wiederherstellung verfügbaren Sammlungen. |
| databaseName |Zeichenfolge| Der Name der für die Wiederherstellung verfügbaren Datenbank. |

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

### <a name="restorablemongodbresourceslistresult"></a><a id="restorablemongodbresourceslistresult"></a>RestorableMongodbResourcesListResult

Die Antwort des Auflistungsvorgangs, die die wiederherstellbaren Ressourcen der Azure Cosmos DB-API für MongoDB enthält.

| **Name** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| value |[DatabaseRestoreResource](#databaserestoreresource)[]| Liste der wiederherstellbaren Ressourcen der Azure Cosmos DB-API für MongoDB, einschließlich der Datenbank- und Sammlungsnamen. |

## <a name="next-steps"></a>Nächste Schritte

* [Auflisten wiederherstellbarer Datenbanken](restorable-mongodb-databases-list.md) in der Azure Cosmos DB-API für MongoDB mithilfe der REST-API
* [Ressourcenmodell](continuous-backup-restore-resource-model.md) des fortlaufenden Sicherungsmodus