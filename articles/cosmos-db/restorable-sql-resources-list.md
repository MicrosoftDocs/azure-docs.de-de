---
title: Auflisten wiederherstellbarer SQL-API-Ressourcen in Azure Cosmos DB mithilfe der REST-API
description: Hier erfahren Sie, wie Sie eine Liste der Datenbank- und Containerkombinationen zurückgeben, die zum angegebenen Zeitstempel und am angegebenen Speicherort im Konto vorhanden sind. Dies ist in Szenarios hilfreich, in denen überprüft werden soll, welche Ressourcen zu einer bestimmten Zeit (Zeitstempel) und an einem bestimmten Speicherort vorhanden sind.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: e31423ae5afd4955ebe7acf1d456496f15f14a6b
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537288"
---
# <a name="list-restorable-sql-api-resources-in-azure-cosmos-db-using-rest-api"></a>Auflisten wiederherstellbarer SQL-API-Ressourcen in Azure Cosmos DB mithilfe der REST-API

> [!IMPORTANT]
> Das Feature der Zeitpunktwiederherstellung (fortlaufender Sicherungsmodus) für Azure Cosmos DB befindet sich zurzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Hier erfahren Sie, wie Sie eine Liste der Datenbank- und Containerkombinationen zurückgeben, die zum angegebenen Zeitstempel und am angegebenen Speicherort im Konto vorhanden sind. Dies ist in Szenarios hilfreich, in denen überprüft werden soll, welche Ressourcen zu einer bestimmten Zeit (Zeitstempel) und an einem bestimmten Speicherort vorhanden sind. Diese API erfordert die `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`-Berechtigung.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlResources?api-version=2020-06-01-preview
```

Mit optionalen Parametern:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlResources?api-version=2020-06-01-preview&amp;restoreLocation={restoreLocation}&amp;restoreTimestampInUtc={restoreTimestampInUtc}
```

## <a name="uri-parameters"></a>URI-Parameter

| Name | Geben Sie in | Erforderlich | type | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |Zeichenfolge| Hierbei handelt es sich um die instanceId-GUID eines wiederherstellbaren Datenbankkontos. |
| **location** | path | True | Zeichenfolge| Hierbei handelt es sich um die Azure Cosmos DB-Region mit Leerzeichen zwischen den Wörtern und Großschreibung der einzelnen Wörter. |
| **subscriptionId** | path | True | Zeichenfolge| Hierbei handelt es sich um die ID des Zielabonnements. |
| **api-version** | Abfrage | True | Zeichenfolge | Hierbei handelt es sich um die für diesen Vorgang zu verwendende API-Version. |
| **restoreLocation** | Abfrage | |Zeichenfolge| Hierbei handelt es sich um den Speicherort, an dem sich die wiederherstellbaren Ressourcen befinden. |
| **restoreTimestampInUtc** | Abfrage | |Zeichenfolge| Hierbei handelt es sich um den Zeitstempel, zu dem die wiederherstellbaren Ressourcen vorhanden waren. |

## <a name="responses"></a>Antworten

| Name | type | BESCHREIBUNG |
| --- | --- | --- |
| 200 – OK | [RestorableSqlResourcesListResult](#restorablesqlresourceslistresult)| Der Vorgang wurde erfolgreich abgeschlossen. |
| Andere Statuscodes | [DefaultErrorResponse](#defaulterrorresponse)| Fehlerantwort mit Beschreibung des Grunds für den Fehler. |

## <a name="examples"></a>Beispiele

### <a name="cosmosdbrestorablesqlresourcelist"></a>CosmosDBRestorableSqlResourceList

**Beispielanforderung**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlResources?api-version=2020-06-01-preview&amp;restoreLocation=WestUS&amp;restoreTimestampInUtc=10/13/2020 4:56
```

**Beispiel für eine Antwort**

Statuscode: 200

```json
{
  "value": [
    {
      "databaseName": "Database1",
      "collectionNames": [
        "Container1"
      ]
    },
    {
      "databaseName": "Database2",
      "collectionNames": [
        "Container1",
        "Container2"
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

|Definition | Beschreibung| | --- || --- | | [DatabaseRestoreResource](#databaserestoreresource) | Bestimmte wiederherzustellende Datenbanken. | | [DefaultErrorResponse](#defaulterrorresponse) | Eine Fehlerantwort vom Dienst. | | [ErrorResponse](#errorresponse) | Fehlerantwort. | | [RestorableSqlResourcesListResult](#restorablesqlresourceslistresult) | Die Antwort des Auflistungsvorgangs, die die wiederherstellbaren SQL-Ressourcen enthält. |

### <a name="databaserestoreresource"></a><a id="databaserestoreresource"></a>DatabaseRestoreResource

Bestimmte wiederherzustellende Datenbanken.

| **Name** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| collectionNames |string[]| Die Namen der zur Wiederherstellung verfügbaren Sammlungen. |
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

### <a name="restorablesqlresourceslistresult"></a><a id="restorablesqlresourceslistresult"></a>RestorableSqlResourcesListResult

Die Antwort des Auflistungsvorgangs, die die wiederherstellbaren SQL-Ressourcen enthält.

| **Name** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| value |[DatabaseRestoreResource](#databaserestoreresource)[]| Liste der wiederherstellbaren SQL-Ressourcen, einschließlich der Datenbank- und Sammlungsnamen. |

## <a name="next-steps"></a>Nächste Schritte

* [Auflisten wiederherstellbarer Datenbanken](restorable-sql-databases-list.md) in der Azure Cosmos SQL-API mithilfe der REST-API
* [Ressourcenmodell](continuous-backup-restore-resource-model.md) des fortlaufenden Sicherungsmodus