---
title: Ressourcenmodell für das Feature „Zeitpunktwiederherstellung“ von Azure Cosmos DB
description: In diesem Artikel wird das Ressourcenmodell für das Feature „Zeitpunktwiederherstellung“ von Azure Cosmos DB erläutert. Beschrieben werden die Parameter zur Unterstützung der fortlaufenden Sicherung und der Ressourcen, die in der Azure Cosmos DB-API für SQL- und MongoDB-Konten wiederhergestellt werden können.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 91421b66af441ed2c7e9c8b66c16ee83f489b03e
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538501"
---
# <a name="resource-model-for-the-azure-cosmos-db-point-in-time-restore-feature-preview"></a>Ressourcenmodell für das Feature „Zeitpunktwiederherstellung“ von Azure Cosmos DB (Vorschau)
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Das Feature „Zeitpunktwiederherstellung“ (fortlaufender Sicherungsmodus) für Azure Cosmos DB befindet sich zurzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In diesem Artikel wird das Ressourcenmodell für das Feature „Zeitpunktwiederherstellung“ von Azure Cosmos DB (Vorschau) erläutert. Beschrieben werden die Parameter zur Unterstützung der fortlaufenden Sicherung und der Ressourcen, die in der Azure Cosmos DB-API für SQL- und MongoDB-Konten wiederhergestellt werden können.

## <a name="database-accounts-resource-model"></a>Ressourcenmodell des Datenbankkontos

Das Ressourcenmodell des Datenbankkontos wird mit einigen zusätzlichen Eigenschaften zur Unterstützung der neuen Wiederherstellungsszenarien aktualisiert. Diese Eigenschaften sind **„BackupPolicy“, „CreateMode“ und „RestoreParameters“.**

### <a name="backuppolicy"></a>BackupPolicy

Eine neue Eigenschaft in der Sicherungsrichtlinie auf Kontoebene mit dem Namen „Type“ unter dem Parameter „backuppolicy“ aktiviert die Funktionen „Fortlaufende Sicherung“ „Zeitpunktwiederherstellung“. Dieser Modus wird als **fortlaufende Sicherung** bezeichnet. In der öffentlichen Vorschau können Sie diesen Modus nur beim Erstellen des Kontos festlegen. Nachdem er aktiviert wurde, sind bei allen in diesem Konto erstellten Container und Datenbanken die Funktionen „Fortlaufende Sicherung“ und „Zeitpunktwiederherstellung“ standardmäßig aktiviert.

> [!NOTE]
> Das Feature „Zeitpunktwiederherstellung“ befindet sich zurzeit in der öffentlichen Vorschau und steht für Azure Cosmos DB-API für MongoDB- und SQL-Konten zur Verfügung. Nachdem Sie ein Konto mit fortlaufendem Modus erstellt haben, können Sie es nicht auf einen regelmäßigen Modus umstellen.

### <a name="createmode"></a>CreateMode

Diese Eigenschaft gibt an, wie das Konto erstellt wurde. Die möglichen Werte sind „Default“ (Standard) und „Restore“ (Wiederherstellen). Legen Sie diesen Wert zum Ausführen einer Wiederherstellung auf „Restore“ fest, und geben Sie die entsprechenden Werte in der Eigenschaft `RestoreParameters` an.

### <a name="restoreparameters"></a>RestoreParameters

Die Ressource `RestoreParameters` enthält die Details zum Wiederherstellungsvorgang, darunter die Konto-ID, die Zeit zum Wiederherstellen und die Ressourcen, die wiederhergestellt werden müssen.

|Eigenschaftenname |BESCHREIBUNG  |
|---------|---------|
|restoreMode  | Der Wiederherstellungsmodus sollte „PointInTime“ lauten. |
|restoreSource   |  Die InstanceId des Quellkontos, aus dem die Wiederherstellung eingeleitet wird.       |
|restoreTimestampInUtc  | Die Zeit in UTC (koordinierte Weltzeit), zu der das Konto wiederhergestellt werden soll. |
|databasesToRestore   | Eine Liste von `DatabaseRestoreSource`-Objekten zur Angabe, welche Datenbanken und Container wiederhergestellt werden sollen. Wenn dieser Wert leer ist, wird das gesamte Konto wiederhergestellt.   |

**DatabaseRestoreResource-**  – Jede Ressource stellt eine einzelne Datenbank und alle Sammlungen in dieser Datenbank dar.

|Eigenschaftenname |BESCHREIBUNG  |
|---------|---------|
|databaseName | den Namen der Datenbank |
| collectionNames| Die Liste von Containern in dieser Datenbank |

### <a name="sample-resource"></a>Beispielressource

Der folgende JSON-Code ist ein Beispiel für eine Datenbankkontoressource mit aktivierter fortlaufender Sicherung:

```json
{
  "location": "westus",
  "properties": {
    "databaseAccountOfferType": "Standard",
    "locations": [
      {
        "failoverPriority": 0,
        "locationName": "southcentralus",
        "isZoneRedundant": false
      }
    ],
    "createMode": "Restore",
    "restoreParameters": {
      "restoreMode": "PointInTime",
      "restoreSource": "/subscriptions/subid/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/1a97b4bb-f6a0-430e-ade1-638d781830cc",
      "restoreTimestampInUtc": "2020-06-11T22:05:09Z",
      "databasesToRestore": [
        {
          "databaseName": "db1",
          "collectionNames": [
            "collection1",
            "collection2"
          ]
        },
        {
          "databaseName": "db2",
          "collectionNames": [
            "collection3",
            "collection4"
          ]
        }
      ]
    },
    "backupPolicy": {
      "type": "Continuous"
    },
}
}
```

## <a name="restorable-resources"></a>Wiederherstellbare Ressourcen

Eine Reihe von neuen Ressourcen und APIs steht zur Verfügung. Damit können Sie wichtige Informationen ermitteln zu Ressourcen, die wiederhergestellt werden können, den Standorten, aus denen sie wiederhergestellt werden können, und den Zeitstempeln, zu denen wichtige Vorgänge für diese Ressourcen ausgeführt wurden.

> [!NOTE]
> Alle zum Auflisten dieser Ressourcen verwendeten APIs erfordern die folgenden Berechtigungen:
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read`

### <a name="restorable-database-account"></a>Wiederherstellbares Datenbankkonto

Diese Ressource enthält eine Instanz eines Datenbankkontos, die wiederhergestellt werden kann. Das Datenbankkonto kann entweder ein gelöschtes oder ein aktives Konto sein. Es enthält Informationen, mit denen Sie das Quelldatenbankkonto suchen können, das Sie wiederherstellen möchten.

|Eigenschaftenname |BESCHREIBUNG  |
|---------|---------|
| id | Der eindeutige Bezeichner der Ressource. |
| .<Name der Region | Der Name des globalen Datenbankkontos. |
| creationTime | Die Zeit in UTC (koordinierte Weltzeit), zu der das Konto erstellt wurde.  |
| deletionTime | Die Zeit in UTC, zu der das Konto gelöscht wurde.  Dieser Wert ist leer, wenn das Konto aktiv ist. |
| apiType | Der API-Typ des Azure Cosmos DB-Kontos. |
| restorableLocations | Die Liste der Standorte, an denen das Konto vorhanden war. |
| restorableLocations: locationName | Der Regionsname des regionalen Kontos. |
| restorableLocations: regionalDatabaseAccountInstanceI | Der GUID des regionalen Kontos. |
| restorableLocations: creationTime | Die Zeit in UTC, zu der das regionale Konto erstellt wurde.|
| restorableLocations: deletionTime | Die Zeit in UTC, zu der das regionale Konto gelöscht wurde. Dieser Wert ist leer, wenn das regionale Konto aktiv ist.|

Eine Liste aller wiederherstellbaren Konten finden Sie in den Artikeln [Wiederherstellbare Datenbankkonten – Auflisten](restorable-database-accounts-list.md) oder [Wiederherstellbare Datenbankkonten – Auflisten nach Standort](restorable-database-accounts-list-by-location.md).

### <a name="restorable-sql-database"></a>Wiederherstellbare SQL-Datenbank

Jede Ressource enthält Informationen zu einem Mutationsereignis, z. B. Erstellung und Löschung, das in der SQL-Datenbank eingetreten ist. Diese Informationen können in Szenarien hilfreich sein, in denen die Datenbank versehentlich gelöscht wurde und Sie herausfinden müssen, wann dieses Ereignis eingetreten ist.

|Eigenschaftenname |BESCHREIBUNG  |
|---------|---------|
| eventTimestamp | Die Zeit in UTC, zu der die Datenbank erstellt oder gelöscht wird. |
| ownerId | Der Name der SQL-Datenbank |
| ownerResourceId | Die Ressourcen-ID der SQL-Datenbank|
| operationType | Der Vorgangstyp dieses Datenbankereignisses. Die folgenden Werte sind möglich:<br/><ul><li>„Create“: Datenbankerstellungsereignis</li><li>„Delete“: Datenbanklöschungsereignis</li><li>„Replace“: Datenbankänderungsereignis</li><li>„SystemOperation“: Ein vom System ausgelöstes Datenbankänderungsereignis. Dieses Ereignis wird nicht durch den Benutzer eingeleitet.</li></ul> |
| database |Die Eigenschaften der SQL-Datenbank zum Zeitpunkt des Ereignisses|

Eine Liste aller Datenbankmutationen finden Sie im Artikel [Wiederherstellbare SQL-Datenbanken – Auflisten](restorable-sql-databases-list.md).

### <a name="restorable-sql-container"></a>Wiederherstellbare SQL-Container

Jede Ressource enthält Informationen zu einem Mutationsereignis, z. B. Erstellung und Löschung, das im SQL-Container eingetreten ist. Diese Informationen können in Szenarien hilfreich sein, in denen der Container geändert oder gelöscht wurde und Sie herausfinden müssen, wann dieses Ereignis eingetreten ist.

|Eigenschaftenname |BESCHREIBUNG  |
|---------|---------|
| eventTimestamp    | Die Zeit in UTC, zu der dieses Containerereignis eingetreten ist.|
| ownerId| Der Name des SQL-Containers.|
| ownerResourceId   | Die Ressourcen-ID des SQL-Containers.|
| operationType | Der Vorgangstyp dieses Containerereignisses. Die folgenden Werte sind möglich: <br/><ul><li>„Create“: Containererstellungsereignis</li><li>„Delete“: Containerlöschungsereignis</li><li>„Replace“: Containeränderungsereignis</li><li>„SystemOperation“: Ein vom System ausgelöstes Containeränderungsereignis. Dieses Ereignis wird nicht durch den Benutzer eingeleitet.</li></ul> |
| Container | Die Eigenschaften des SQL-Containers zum Zeitpunkt des Ereignisses.|

Eine Liste aller Containermutationen in derselben Datenbank finden Sie im Artikel [Wiederherstellbare SQL-Container – Auflisten](restorable-sql-containers-list.md).

### <a name="restorable-sql-resources"></a>Wiederherstellbare SQL-Ressourcen

Jede Ressource stellt eine einzelne Datenbank und alle Container in dieser Datenbank dar.

|Eigenschaftenname |BESCHREIBUNG  |
|---------|---------|
| databaseName  | Der Name der SQL-Datenbank
| collectionNames   | Die Liste von SQL-Containern in dieser Datenbank.|

Eine Liste von Kombinationen aus SQL-Datenbank und -Containern, die für das Konto zum angegebenen Zeitstempel und am angegebenen Standort vorhanden sind, finden Sie im Artikel [Wiederherstellbare SQL-Ressourcen – Auflisten](restorable-sql-resources-list.md).

### <a name="restorable-mongodb-database"></a>Wiederherstellbare MongoDB-Datenbank

Jede Ressource enthält Informationen zu einem Mutationsereignis, z. B. Erstellung und Löschung, das in der MongoDB-Datenbank eingetreten ist. Diese Informationen können in dem Szenario hilfreich sein, in dem die Datenbank versehentlich gelöscht wurde und der Benutzer herausfinden muss, wann dieses Ereignis eingetreten ist.

|Eigenschaftenname |BESCHREIBUNG  |
|---------|---------|
|eventTimestamp| Die Zeit in UTC, zu dem dieses Datenbankereignis eingetreten ist.|
| ownerId| Der Name der MongoDB-Datenbank. |
| ownerResourceId   | Die Ressourcen-ID der MongoDB-Datenbank. |
| operationType |   Der Vorgangstyp dieses Datenbankereignisses. Die folgenden Werte sind möglich:<br/><ul><li> „Create“: Datenbankerstellungsereignis</li><li> „Delete“: Datenbanklöschungsereignis</li><li> „Replace“: Datenbankänderungsereignis</li><li> „SystemOperation“: Ein vom System ausgelöstes Datenbankänderungsereignis. Dieses Ereignis wird nicht durch den Benutzer eingeleitet. </li></ul> |

Eine Liste aller Datenbankmutationen finden Sie im Artikel [Wiederherstellbare MongoDB-Datenbanken – Auflisten](restorable-mongodb-databases-list.md).

### <a name="restorable-mongodb-collection"></a>Wiederherstellbare MongoDB-Sammlung

Jede Ressource enthält Informationen zu einem Mutationsereignis, z. B. Erstellung und Löschung, das in der MongoDB-Sammlung eingetreten ist. Diese Informationen können in Szenarien hilfreich sein, in denen die Sammlung geändert oder gelöscht wurde und der Benutzer herausfinden muss, wann dieses Ereignis eingetreten ist.

|Eigenschaftenname |BESCHREIBUNG  |
|---------|---------|
| eventTimestamp |Die Zeit in UTC, zu der dieses Sammlungsereignis eingetreten ist. |
| ownerId| Der Name der MongoDB-Sammlung. |
| ownerResourceId   | Die Ressourcen-ID der MongoDB-Sammlung. |
| operationType |Der Vorgangstyp dieses Sammlungsereignisses. Die folgenden Werte sind möglich:<br/><ul><li>„Create“: Sammlungserstellungsereignis</li><li>„Delete“: Sammlungslöschungsereignis</li><li>„Replace“: Sammlungsänderungsereignis</li><li>„SystemOperation“: Ein vom System ausgelöstes Sammlungsänderungsereignis. Dieses Ereignis wird nicht durch den Benutzer eingeleitet.</li></ul> |

Eine Liste aller Containermutationen in derselben Datenbank finden Sie im Artikel [Wiederherstellbare MongoDB-Sammlungen – Auflisten](restorable-mongodb-collections-list.md).

### <a name="restorable-mongodb-resources"></a>Wiederherstellbare MongoDB-Ressourcen

Jede Ressource stellt eine einzelne Datenbank und alle Sammlungen in dieser Datenbank dar.

|Eigenschaftenname |BESCHREIBUNG  |
|---------|---------|
| databaseName  |Der Name der MongoDB-Datenbank. |
| collectionNames | Die Liste der MongoDB-Sammlungen in dieser Datenbank. |

Eine Liste aller Kombinationen aus MongoDB-Datenbank und -Sammlung, die für das Konto zum angegebenen Zeitstempel und am angegebenen Standort vorhanden sind, finden Sie im Artikel [Wiederherstellbare MongoDB-Ressourcen – Auflisten](restorable-mongodb-resources-list.md).

## <a name="next-steps"></a>Nächste Schritte

* Konfigurieren und verwalten Sie die fortlaufende Sicherung über das [Azure-Portal](continuous-backup-restore-portal.md) oder aber mithilfe von [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md) oder [Azure Resource Manager](continuous-backup-restore-template.md).
* [Verwalten Sie Berechtigungen](continuous-backup-restore-permissions.md), die zum Wiederherstellen von Daten mit dem fortlaufenden Sicherungsmodus erforderlich sind.
