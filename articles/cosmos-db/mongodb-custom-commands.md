---
title: MongoDB-Erweiterungsbefehle zum Verwalten von Daten in der Azure Cosmos DB-API für MongoDB
description: In diesem Artikel wird beschrieben, wie Sie MongoDB-Erweiterungsbefehle zum Verwalten von Daten verwenden, die in der API für MongoDB von Azure Cosmos DB gespeichert sind.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 05/28/2020
ms.author: chrande
ms.custom: devx-track-js
ms.openlocfilehash: 2fd2fa7620e57c58f72dad73c1012a19190e8fbc
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359645"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Verwenden von MongoDB-Erweiterungsbefehlen zum Verwalten von Daten in der API für MongoDB von Azure Cosmos DB 
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Das folgende Dokument enthält die benutzerdefinierten Aktionsbefehle, die für die Azure Cosmos DB-API für MongoDB spezifisch sind. Diese Befehle können zum Erstellen und Abrufen von Datenbankressourcen verwendet werden, die für das [Azure Cosmos DB-Kapazitätsmodell](account-databases-containers-items.md) spezifisch sind.

Mit der API für MongoDB von Azure Cosmos DB kommen Sie in den Genuss der Vorteile von Cosmos DB, z. B. globale Verteilung, automatisches Sharding, Hochverfügbarkeit, Latenzgarantien, automatische Verschlüsselung im ruhenden Zustand, Sicherungen und vieles mehr, während gleichzeitig Ihre Investitionen in Ihre MongoDB-App geschützt werden. Sie können mit der API für MongoDB von Azure Cosmos DB über einen der Open-Source-[MongoDB-Clienttreiber](https://docs.mongodb.org/ecosystem/drivers) kommunizieren. Die API für MongoDB von Azure Cosmos DB ermöglicht die Verwendung vorhandener Clienttreiber über das [Wire Protocol von MongoDB](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

## <a name="mongodb-protocol-support"></a>Protokollunterstützung für MongoDB

Die Azure Cosmos DB-API für MongoDB ist mit der MongoDB-Serverversion 3.2 und 3.6 kompatibel. Ausführliche Einzelheiten finden Sie unter [Unterstützte Features und Syntax](mongodb-feature-support.md). 

Die folgenden Erweiterungsbefehle bieten die Möglichkeit, Azure Cosmos DB-spezifische Ressourcen über Datenbankanforderungen zu erstellen und zu modifizieren:

* [Erstellen der Datenbank](#create-database)
* [Aktualisieren der Datenbank](#update-database)
* [Abrufen der Datenbank](#get-database)
* [Erstellen der Sammlung](#create-collection)
* [Aktualisieren der Sammlung](#update-collection)
* [Abrufen der Sammlung](#get-collection)

## <a name="create-database"></a><a id="create-database"></a> Erstellen der Datenbank

Mit dem Erweiterungsbefehl „CreateDatabase“ wird eine neue MongoDB-Datenbank erstellt. Der Name der Datenbank kann aus dem Datenbankkontext abgerufen verwendet, der mit dem Befehl `use database` festgelegt wird. In der folgenden Tabelle sind die Parameter des Befehls beschrieben:

|**Feld**|**Typ** |**Beschreibung** |
|---------|---------|---------|
| `customAction`   |  `string`  |   Name des benutzerdefinierten Befehls. Er muss „CreateDatabase“ lauten.      |
| `offerThroughput` | `int`  | Bereitgestellter Durchsatz, den Sie für die Datenbank festgelegt haben. Dieser Parameter ist optional. |
| `autoScaleSettings` | `Object` | Für [Modus „Autoskalierung“](provision-throughput-autoscale.md) erforderlich. Dieses Objekt enthält die mit dem Kapazitätsmodus „Autoskalierung“ festgelegten Einstellungen. Sie können den Wert `maxThroughput` einrichten, der die höchste Anzahl von Anforderungseinheiten angibt, auf die die Sammlung dynamisch erhöht wird. |

### <a name="output"></a>Ausgabe

Bei Erfolg gibt der Befehl Folgendes zurück:

```javascript
{ "ok" : 1 }
```

Informationen hierzu finden Sie in der [Standardausgabe](#default-output) des benutzerdefinierten Befehls für die Parameter in der Ausgabe.

### <a name="examples"></a>Beispiele

#### <a name="create-a-database"></a>Erstellen einer Datenbank

Um eine Datenbank mit dem Namen `"test"` mit sämtlichen Standardwerten zu erstellen, verwenden Sie den folgenden Befehl:

```javascript
use test
db.runCommand({customAction: "CreateDatabase"});
```

Mit diesem Befehl wird eine Datenbank ohne Durchsatz auf Datenbankebene erstellt. Das bedeutet, dass die Sammlungen innerhalb dieser Datenbank den Durchsatz angeben müssen, den Sie benötigen.

#### <a name="create-a-database-with-throughput"></a>Erstellen einer Datenbank mit Durchsatz

Um eine Datenbank mit dem Namen `"test"` zu erstellen und einen auf [Datenbankebene](set-throughput.md#set-throughput-on-a-database) bereitgestellten Durchsatz von 1.000 RUs anzugeben, nutzen Sie den folgenden Befehl:

```javascript
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

Dadurch wird eine Datenbank erstellt und ein Durchsatz für sie festgelegt. Alle Sammlungen innerhalb dieser Datenbank teilen sich den festgelegten Durchsatz, es sei denn, die Sammlungen werden mit [einer bestimmten Durchsatzebene](set-throughput.md#set-throughput-on-a-database-and-a-container) erstellt.

#### <a name="create-a-database-with-autoscale-throughput"></a>Erstellen einer Datenbank mit auf „Autoskalierung“ festgelegtem Durchsatz

Um eine Datenbank mit dem Namen `"test"` zu erstellen und einen per Autoskalierung festgelegten maximalen Durchsatz von 20.000 RU/s auf [Datenbankebene](set-throughput.md#set-throughput-on-a-database) anzugeben, verwenden Sie den folgenden Befehl:

```javascript
use test
db.runCommand({customAction: "CreateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```

## <a name="update-database"></a><a id="update-database"></a> Aktualisieren der Datenbank

Mit dem Erweiterungsbefehl „UpdateDatabase“ werden die Eigenschaften aktualisiert, die der angegebenen Datenbank zugeordnet sind. In der folgenden Tabelle sind die Parameter des Befehls beschrieben:

|**Feld**|**Typ** |**Beschreibung** |
|---------|---------|---------|
| `customAction`    |    `string`     |   Der Name des benutzerdefinierten Befehls. Er muss „UpdateDatabase“ lauten.      |
|  `offerThroughput`   |  `int`       |     Neuer bereitgestellter Durchsatz, den Sie für die Datenbank festlegen möchten, wenn die Datenbank [Durchsatz auf Datenbankebene](set-throughput.md#set-throughput-on-a-database) nutzt  |
| `autoScaleSettings` | `Object` | Für [Modus „Autoskalierung“](provision-throughput-autoscale.md) erforderlich. Dieses Objekt enthält die mit dem Kapazitätsmodus „Autoskalierung“ festgelegten Einstellungen. Sie können den Wert `maxThroughput` einrichten, der die höchste Anzahl von Anforderungseinheiten angibt, auf die die Datenbank dynamisch erhöht wird. |

Dieser Befehl verwendet die im Kontext der Sitzung angegebene Datenbank. Dies ist die Datenbank, die Sie im Befehl `use <database>` verwendet haben. Momentan kann der Datenbankname mit diesem Befehl nicht geändert werden.

### <a name="output"></a>Ausgabe

Bei Erfolg gibt der Befehl Folgendes zurück:

```javascript
{ "ok" : 1 }
```

Informationen hierzu finden Sie in der [Standardausgabe](#default-output) des benutzerdefinierten Befehls für die Parameter in der Ausgabe.

### <a name="examples"></a>Beispiele

#### <a name="update-the-provisioned-throughput-associated-with-a-database"></a>Aktualisieren des bereitgestellten Durchsatzes einer Datenbank

Verwenden Sie den folgenden Befehl, um den bereitgestellten Durchsatz einer Datenbank mit dem Namen `"test"` auf 1.200 RUs zu aktualisieren:

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

#### <a name="update-the-autoscale-throughput-associated-with-a-database"></a>Aktualisieren des per Autoskalierung bereitgestellten Durchsatzes einer Datenbank

Um den bereitgestellten Durchsatz einer Datenbank namens `"test"` auf 20.000 RUs zu aktualisieren oder in eine [Durchsatzebene mit Autoskalierung](provision-throughput-autoscale.md) umzuwandeln, nutzen Sie den folgenden Befehl:

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```


## <a name="get-database"></a><a id="get-database"></a> Abrufen der Datenbank

Mit dem Erweiterungsbefehl „GetDatabase“ wird das Datenbankobjekt zurückgegeben. Der Name der Datenbank wird aus dem Datenbankkontext abgerufen, unter dem der Befehl ausgeführt wird.

```javascript
{
  customAction: "GetDatabase"
}
```

In der folgenden Tabelle sind die Parameter des Befehls beschrieben:


|**Feld**|**Typ** |**Beschreibung** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   Der Name des benutzerdefinierten Befehls. Er muss „GetDatabase“ lauten.|
        
### <a name="output"></a>Ausgabe

Wenn der Befehl erfolgreich ist, enthält die Antwort ein Dokument mit den folgenden Feldern:

|**Feld**|**Typ** |**Beschreibung** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Status der Antwort. 1 == Erfolgreich. 0 == Fehler.      |
| `database`    |    `string`        |   Der Name der Datenbank.      |
|   `provisionedThroughput`  |    `int`      |    Bereitgestellter Durchsatz, den Sie für die Datenbank festlegen möchten, wenn die Datenbank [manuellen Durchsatz auf Datenbankebene](set-throughput.md#set-throughput-on-a-database) nutzt     |
| `autoScaleSettings` | `Object` | Dieses Objekt enthält die Kapazitätsparameter, die der Datenbank zugeordnet sind, wenn der Modus [Autoskalierung](provision-throughput-autoscale.md) verwendet wird. Der Wert `maxThroughput` beschreibt die höchste Anzahl von Anforderungseinheiten, auf die die Datenbank dynamisch erhöht wird. |

Wenn der Befehl nicht erfolgreich ist, wird eine Standardantwort für den benutzerdefinierten Befehl zurückgegeben. Informationen hierzu finden Sie in der [Standardausgabe](#default-output) des benutzerdefinierten Befehls für die Parameter in der Ausgabe.

### <a name="examples"></a>Beispiele

#### <a name="get-the-database"></a>Abrufen der Datenbank

Verwenden Sie den folgenden Befehl, um das Datenbankobjekt für eine Datenbank mit dem Namen `"test"` abzurufen:

```javascript
use test
db.runCommand({customAction: "GetDatabase"});
```

Wenn der Datenbank kein Durchsatz zugeordnet ist, lautet die Ausgabe wie folgt:

```javascript
{ "database" : "test", "ok" : 1 }
```

Wenn der Datenbank ein [manueller Durchsatz auf Datenbankebene](set-throughput.md#set-throughput-on-a-database) zugeordnet ist, weist die Ausgabe diese `provisionedThroughput`-Werte auf:

```javascript
{ "database" : "test", "provisionedThroughput" : 20000, "ok" : 1 }
```

Wenn der Datenbank ein [per Autoskalierung festgelegter Durchsatz auf Datenbankebene](provision-throughput-autoscale.md) zugeordnet ist, enthält die Ausgabe `provisionedThroughput` mit Angabe der minimalen RU/s für die Datenbank und `autoScaleSettings` einschließlich `maxThroughput` mit der Angabe der maximalen RU/s für die Datenbank.

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
                "maxThroughput" : 20000
        },
        "ok" : 1
}
```

## <a name="create-collection"></a><a id="create-collection"></a> Erstellen der Sammlung

Mit dem Erweiterungsbefehl „CreateCollection“ wird eine neue MongoDB-Sammlung erstellt. Der Name der Datenbank wird aus dem Datenbankkontext abgerufen, der mit dem Befehl `use database` festgelegt wird. Das Format des Befehls „CreateCollection“ lautet:

```javascript
{
  customAction: "CreateCollection",
  collection: "<Collection Name>",
  shardKey: "<Shard key path>",
  offerThroughput: (int), // Amount of throughput allocated to a specific collection

}
```

In der folgenden Tabelle sind die Parameter des Befehls beschrieben:

| **Feld** | **Typ** | **Erforderlich** | **Beschreibung** |
|---------|---------|---------|---------|
| `customAction` | `string` | Erforderlich | Der Name des benutzerdefinierten Befehls. Er muss „CreateCollection“ lauten.|
| `collection` | `string` | Erforderlich | Name der Sammlung Sonder- oder Leerzeichen sind unzulässig.|
| `offerThroughput` | `int` | Optional | Bereitgestellter Durchsatz, der für die Datenbank festgelegt werden soll. Wenn dieser Parameter nicht angegeben wird, wird standardmäßig der Mindestwert 400 RU/s verwendet. *Um einen Durchsatz über 10.000 RU/s hinaus anzugeben, ist der Parameter `shardKey` erforderlich.|
| `shardKey` | `string` | Für Sammlungen mit hohem Durchsatz erforderlich | Der Pfad des Shardschlüssels für die horizontal partitionierte Sammlung. Dieser Parameter ist erforderlich, wenn Sie mehr als 10.000 RU/s in `offerThroughput` festlegen.  Falls angegeben, erfordern alle eingefügten Dokumente diesen Schlüssel und Wert. |
| `autoScaleSettings` | `Object` | Für [Modus „Autoskalierung“](provision-throughput-autoscale.md) erforderlich. | Dieses Objekt enthält die mit dem Kapazitätsmodus „Autoskalierung“ festgelegten Einstellungen. Sie können den Wert `maxThroughput` einrichten, der die höchste Anzahl von Anforderungseinheiten angibt, auf die die Sammlung dynamisch erhöht wird. |

### <a name="output"></a>Ausgabe

Gibt eine Standardantwort für den benutzerdefinierten Befehl zurück. Informationen hierzu finden Sie in der [Standardausgabe](#default-output) des benutzerdefinierten Befehls für die Parameter in der Ausgabe.

### <a name="examples"></a>Beispiele

#### <a name="create-a-collection-with-the-minimum-configuration"></a>Erstellen einer Sammlung mit der Mindestkonfiguration

Um eine neue Sammlung mit dem Namen `"testCollection"` und den Standardwerten zu erstellen, geben Sie den folgenden Befehl an: 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection"});
```

Das Ergebnis ist eine neue feste Sammlung ohne Shards mit 400 RU/s und einem automatisch erstellten Index für das Feld `_id`. Diese Art der Konfiguration gilt auch bei der Erstellung neuer Sammlungen über die Funktion `insert()`. Beispiel: 

```javascript
use test
db.newCollection.insert({});
```

#### <a name="create-a-unsharded-collection"></a>Erstellen einer Sammlung ohne Shards

Verwenden Sie den folgenden Befehl, um eine Sammlung ohne Shards mit dem Namen `"testCollection"` und einem bereitgestellten Durchsatz von 1.000 RUs zu erstellen: 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

Sie können eine Sammlung mit bis zu 10.000 RU/s als `offerThroughput` erstellen, ohne einen Shardschlüssel angeben zu müssen. Weitere Informationen zu Sammlungen mit größerem Durchsatz finden Sie im nächsten Abschnitt.

#### <a name="create-a-sharded-collection"></a>Erstellen einer Sammlung mit Shards

Verwenden Sie den folgenden Befehl, um eine Sammlung mit Shards mit dem Namen `"testCollection"`, einem bereitgestellten Durchsatz von 11.000 RU/s und der `shardkey`-Eigenschaft „a.b“ zu erstellen:

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 11000, shardKey: "a.b" });
```

Dieser Befehl erfordert jetzt den Parameter `shardKey`, da mehr als 10.000 RU/s in `offerThroughput` angegeben sind.

#### <a name="create-an-unsharded-autoscale-collection"></a>Erstellen einer Sammlung mit Autoskalierung ohne Shards

Um eine Sammlung ohne Shards namens `'testCollection'` zu erstellen, deren [Durchsatzkapazität per Autoskalierung](provision-throughput-autoscale.md) auf 4.000 RU/s festgelegt ist, verwenden Sie den folgenden Befehl:

```javascript
use test
db.runCommand({ 
    customAction: "CreateCollection", collection: "testCollection", 
    autoScaleSettings:{
      maxThroughput: 4000
    } 
});
```

Für den Wert `autoScaleSettings.maxThroughput` können Sie einen Bereich von 4.000 bis 10.000 RU/s ohne Shardschlüssel angeben. Für einen höheren Durchsatz bei der Autoskalierung müssen Sie den Parameter `shardKey` angeben.

#### <a name="create-a-sharded-autoscale-collection"></a>Erstellen einer Sammlung mit Autoskalierung mit Shards

Um eine Sammlung mit Shards namens `'testCollection'` mit dem Shardschlüssel `'a.b'` zu erstellen, deren [Durchsatzkapazität per Autoskalierung](provision-throughput-autoscale.md) auf 20.000 RU/s festgelegt ist, verwenden Sie den folgenden Befehl:

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", shardKey: "a.b", autoScaleSettings: { maxThroughput: 20000 }});
```

## <a name="update-collection"></a><a id="update-collection"></a> Aktualisieren der Sammlung

Mit dem Erweiterungsbefehl „UpdateCollection“ werden die Eigenschaften aktualisiert, die der angegebenen Sammlung zugeordnet sind.

```javascript
{
  customAction: "UpdateCollection",
  collection: "<Name of the collection that you want to update>",
  offerThroughput: (int) // New throughput that will be set to the collection
}
```

In der folgenden Tabelle sind die Parameter des Befehls beschrieben:

|**Feld**|**Typ** |**Beschreibung** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   Der Name des benutzerdefinierten Befehls. Er muss „UpdateCollection“ lauten.      |
|  `collection`   |   `string`      |   Name der Sammlung       |
| `offerThroughput` | `int` |   Bereitgestellter Durchsatz, der für die Sammlung festgelegt wird.|
| `autoScaleSettings` | `Object` | Für [Modus „Autoskalierung“](provision-throughput-autoscale.md) erforderlich. Dieses Objekt enthält die mit dem Kapazitätsmodus „Autoskalierung“ festgelegten Einstellungen. Der Wert `maxThroughput` beschreibt die höchste Anzahl von Anforderungseinheiten, auf die die Sammlung dynamisch erhöht wird. |

## <a name="output"></a>Ausgabe

Gibt eine Standardantwort für den benutzerdefinierten Befehl zurück. Informationen hierzu finden Sie in der [Standardausgabe](#default-output) des benutzerdefinierten Befehls für die Parameter in der Ausgabe.

### <a name="examples"></a>Beispiele

#### <a name="update-the-provisioned-throughput-associated-with-a-collection"></a>Aktualisieren des bereitgestellten Durchsatzes einer Sammlung

Verwenden Sie den folgenden Befehl, um den bereitgestellten Durchsatz einer Sammlung mit dem Namen `"testCollection"` auf 1.200 RU/s zu aktualisieren:

```javascript
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a> Abrufen der Sammlung

Mit dem benutzerdefinierten Befehl „GetCollection“ wird das Sammlungsobjekt zurückgegeben.

```javascript
{
  customAction: "GetCollection",
  collection: "<Name of the collection>"
}
```

In der folgenden Tabelle sind die Parameter des Befehls beschrieben:


|**Feld**|**Typ** |**Beschreibung** |
|---------|---------|---------|
| `customAction`    |   `string`      |   Der Name des benutzerdefinierten Befehls. Er muss „GetCollection“ lauten.      |
| `collection`    |    `string`     |    Name der Sammlung     |

### <a name="output"></a>Ausgabe

Wenn der Befehl erfolgreich ist, enthält die Antwort ein Dokument mit den folgenden Feldern:


|**Feld**|**Typ** |**Beschreibung** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Status der Antwort. 1 == Erfolgreich. 0 == Fehler.      |
| `database`    |    `string`     |   Der Name der Datenbank.      |
| `collection`    |    `string`     |    Name der Sammlung     |
|  `shardKeyDefinition`   |   `document`      |  Dokument mit Indexspezifikation zur Verwendung als Shardschlüssel. Dies ist ein optionaler Antwortparameter.       |
|  `provisionedThroughput`   |   `int`      |    Bereitgestellter Durchsatz, der für die Sammlung festgelegt werden soll. Dies ist ein optionaler Antwortparameter.     |
| `autoScaleSettings` | `Object` | Dieses Objekt enthält die Kapazitätsparameter, die der Datenbank zugeordnet sind, wenn der Modus [Autoskalierung](provision-throughput-autoscale.md) verwendet wird. Der Wert `maxThroughput` beschreibt die höchste Anzahl von Anforderungseinheiten, auf die die Sammlung dynamisch erhöht wird. |

Wenn der Befehl nicht erfolgreich ist, wird eine Standardantwort für den benutzerdefinierten Befehl zurückgegeben. Informationen hierzu finden Sie in der [Standardausgabe](#default-output) des benutzerdefinierten Befehls für die Parameter in der Ausgabe.

### <a name="examples"></a>Beispiele

#### <a name="get-the-collection"></a>Abrufen der Sammlung

Verwenden Sie den folgenden Befehl, um das Sammlungsobjekt für eine Sammlung mit dem Namen `"testCollection"` abzurufen:

```javascript
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

Wenn der Sammlung eine Durchsatzkapazität zugeordnet ist, enthält sie den Wert `provisionedThroughput`, und die Ausgabe ist wie folgt:

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 400,
        "ok" : 1
}
```

Wenn die Sammlung einen per Autoskalierung festgelegten Durchsatz hat, enthält sie das Objekt `autoScaleSettings` mit dem Parameter `maxThroughput`, der den maximalen Durchsatz definiert, auf den die Sammlung dynamisch erhöht wird. Zusätzlich enthält sie auch den Wert `provisionedThroughput`, der den Mindestdurchsatz bestimmt, auf den diese Sammlung reduziert wird, wenn keine Anforderungen in der Sammlung vorhanden sind: 

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 1000,
        "autoScaleSettings" : {
            "maxThroughput" : 10000
        },
        "ok" : 1
}
```

Wenn die Sammlung einen [Durchsatz auf Datenbankebene](set-throughput.md#set-throughput-on-a-database) gemeinsam nutzt, entweder im Modus „Autoskalierung“ oder manuell, lautet die Ausgabe so:

```javascript
{ "database" : "test", "collection" : "testCollection", "ok" : 1 }
```

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
            "maxThroughput" : 20000
        },
        "ok" : 1
}
```


## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a> Standardausgabe eines benutzerdefinierten Befehls

Wenn nichts anderes angegeben ist, enthält eine benutzerdefinierte Antwort ein Dokument mit den folgenden Feldern:

|**Feld**|**Typ** |**Beschreibung** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Status der Antwort. 1 == Erfolgreich. 0 == Fehler.      |
| `code`    |   `int`      |   Wird nur zurückgegeben, wenn der Befehl nicht erfolgreich ist (ok == 0). Enthält den MongoDB-Fehlercode. Dies ist ein optionaler Antwortparameter.      |
|  `errMsg`   |  `string`      |    Wird nur zurückgegeben, wenn der Befehl nicht erfolgreich ist (ok == 0). Enthält eine benutzerfreundliche Fehlermeldung. Dies ist ein optionaler Antwortparameter.      |

Beispiel:

```javascript
{ "ok" : 1 }
```

## <a name="next-steps"></a>Nächste Schritte

Sie können sich nun über die folgenden Azure Cosmos DB-Konzepte informieren: 

* [Indizierung in Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Festlegen einer Gültigkeitsdauer für den automatischen Ablauf von Daten in Azure Cosmos DB](../cosmos-db/time-to-live.md)
