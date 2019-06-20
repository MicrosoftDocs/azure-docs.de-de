---
title: MongoDB-Erweiterungsbefehle zum Verwalten von Daten in der API für MongoDB von Azure Cosmos DB
description: In diesem Artikel wird beschrieben, wie Sie MongoDB-Erweiterungsbefehle zum Verwalten von Daten verwenden, die in der API für MongoDB von Azure Cosmos DB gespeichert sind.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: 94b1048befc8716caf5f7f51adb1f95d047d4077
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64925655"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Verwenden von MongoDB-Erweiterungsbefehlen zum Verwalten von Daten in der API für MongoDB von Azure Cosmos DB 

Azure Cosmos DB ist ein global verteilter Datenbankdienst von Microsoft mit mehreren Modellen. Sie können mit der API für MongoDB von Azure Cosmos DB über einen der Open-Source-[MongoDB-Clienttreiber](https://docs.mongodb.org/ecosystem/drivers) kommunizieren. Die API für MongoDB von Azure Cosmos DB ermöglicht die Verwendung vorhandener Clienttreiber über das [Wire Protocol von MongoDB](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Mit der API für MongoDB von Azure Cosmos DB kommen Sie in den Genuss der Vorteile von Cosmos DB, z. B. globale Verteilung, automatisches Sharding, Hochverfügbarkeit, Latenzgarantien, automatische Verschlüsselung im ruhenden Zustand, Sicherungen und vieles mehr, während gleichzeitig Ihre Investitionen in Ihre MongoDB-App geschützt werden.

## <a name="mongodb-protocol-support"></a>Protokollunterstützung für MongoDB

Standardmäßig ist die API für MongoDB von Azure Cosmos DB mit der MongoDB-Serverversion 3.2 kompatibel. Ausführlichere Informationen finden Sie im Artikel zu den [unterstützten Features und zur Syntax](mongodb-feature-support.md). Die in MongoDB Version 3.4 hinzugefügten Features und Abfrageoperatoren sind derzeit als Vorschauversion in der API für MongoDB von Azure Cosmos DB verfügbar. Die folgenden Erweiterungsbefehle unterstützen spezifische Funktionen von Azure Cosmos DB, wenn für die Daten, die unter der API für MongoDB von Azure Cosmos DB gespeichert sind, CRUD-Vorgänge durchgeführt werden:

* [Erstellen der Datenbank](#create-database)
* [Aktualisieren der Datenbank](#update-database)
* [Abrufen der Datenbank](#get-database)
* [Erstellen der Sammlung](#create-collection)
* [Aktualisieren der Sammlung](#update-collection)
* [Abrufen der Sammlung](#get-collection)

## <a id="create-database"></a> Erstellen der Datenbank

Mit dem Erweiterungsbefehl „CreateDatabase“ wird eine neue MongoDB-Datenbank erstellt. Der Name der Datenbank wird aus dem Datenbankkontext verwendet, unter dem der Befehl ausgeführt wird. Das Format des Befehls „CreateDatabase“ lautet:

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

In der folgenden Tabelle sind die Parameter des Befehls beschrieben:

|**Feld**|**Typ** |**Beschreibung** |
|---------|---------|---------|
| customAction   |  Zeichenfolge  |   Name des benutzerdefinierten Befehls. Er muss „CreateDatabase“ lauten.      |
| offerThroughput | int  | Bereitgestellter Durchsatz, den Sie für die Datenbank festgelegt haben. Dieser Parameter ist optional. |

### <a name="output"></a>Output

Gibt eine Standardantwort für den benutzerdefinierten Befehl zurück. Informationen hierzu finden Sie in der [Standardausgabe](#default-output) des benutzerdefinierten Befehls für die Parameter in der Ausgabe.

### <a name="examples"></a>Beispiele

**Erstellen einer Datenbank**

Verwenden Sie den folgenden Befehl, um eine Datenbank mit dem Namen „test“ zu erstellen:

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**Erstellen einer Datenbank und Bereitstellen eines Durchsatzes**

Verwenden Sie den folgenden Befehl, um eine Datenbank mit dem Namen „test“ und einem bereitgestellten Durchsatz von 1.000 RUs zu erstellen:

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a id="update-database"></a> Aktualisieren der Datenbank

Mit dem Erweiterungsbefehl „UpdateDatabase“ werden die Eigenschaften aktualisiert, die der angegebenen Datenbank zugeordnet sind. Derzeit können Sie nur die „offerThroughput“-Eigenschaft aktualisieren.

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

In der folgenden Tabelle sind die Parameter des Befehls beschrieben:

|**Feld**|**Typ** |**Beschreibung** |
|---------|---------|---------|
| customAction    |    Zeichenfolge     |   Der Name des benutzerdefinierten Befehls. Er muss „UpdateDatabase“ lauten.      |
|  offerThroughput   |  int       |     Neuer bereitgestellter Durchsatz, den Sie für die Datenbank festlegen möchten.    |

### <a name="output"></a>Output

Gibt eine Standardantwort für den benutzerdefinierten Befehl zurück. Informationen hierzu finden Sie in der [Standardausgabe](#default-output) des benutzerdefinierten Befehls für die Parameter in der Ausgabe.

### <a name="examples"></a>Beispiele

**Aktualisieren des bereitgestellten Durchsatzes für eine Datenbank**

Verwenden Sie den folgenden Befehl, um den bereitgestellten Durchsatz einer Datenbank mit dem Namen „test“ auf 1.200 RUs zu aktualisieren:

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a id="get-database"></a> Abrufen der Datenbank

Mit dem Erweiterungsbefehl „GetDatabase“ wird das Datenbankobjekt zurückgegeben. Der Name der Datenbank wird aus dem Datenbankkontext verwendet, unter dem der Befehl ausgeführt wird.

```
{
  customAction: "GetDatabase"
}
```

In der folgenden Tabelle sind die Parameter des Befehls beschrieben:


|**Feld**|**Typ** |**Beschreibung** |
|---------|---------|---------|
|  customAction   |   Zeichenfolge      |   Der Name des benutzerdefinierten Befehls. Er muss „GetDatabase“ lauten.|
        
### <a name="output"></a>Output

Wenn der Befehl erfolgreich ist, enthält die Antwort ein Dokument mit den folgenden Feldern:

|**Feld**|**Typ** |**Beschreibung** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Status der Antwort. 1 == Erfolgreich. 0 == Fehler.      |
| `database`    |    `string`        |   Der Name der Datenbank.      |
|   `provisionedThroughput`  |    `int`      |    Bereitgestellter Durchsatz, der für die Datenbank festgelegt wurde. Dies ist ein optionaler Antwortparameter.     |

Wenn der Befehl nicht erfolgreich ist, wird eine Standardantwort für den benutzerdefinierten Befehl zurückgegeben. Informationen hierzu finden Sie in der [Standardausgabe](#default-output) des benutzerdefinierten Befehls für die Parameter in der Ausgabe.

### <a name="examples"></a>Beispiele

**Abrufen der Datenbank**

Verwenden Sie den folgenden Befehl, um das Datenbankobjekt für eine Datenbank mit dem Namen „test“ abzurufen:

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a id="create-collection"></a> Erstellen der Sammlung

Mit dem Erweiterungsbefehl „CreateCollection“ wird eine neue MongoDB-Sammlung erstellt. Der Name der Datenbank wird aus dem Datenbankkontext verwendet, unter dem der Befehl ausgeführt wird. Das Format des Befehls „CreateCollection“ lautet:

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

In der folgenden Tabelle sind die Parameter des Befehls beschrieben:

|**Feld**|**Typ** |**Beschreibung** |
|---------|---------|---------|
| customAction    | Zeichenfolge | Der Name des benutzerdefinierten Befehls. Er muss „CreateCollection“ lauten.     |
| collection      | Zeichenfolge | Name der Sammlung                                   |
| offerThroughput | int    | Bereitgestellter Durchsatz, der für die Datenbank festgelegt werden soll. Dies ist ein optionaler Parameter. |
| shardKey        | Zeichenfolge | Pfad zum Shardschlüssel für die Erstellung einer Sammlung mit Shards. Dies ist ein optionaler Parameter. |

### <a name="output"></a>Output

Gibt eine Standardantwort für den benutzerdefinierten Befehl zurück. Informationen hierzu finden Sie in der [Standardausgabe](#default-output) des benutzerdefinierten Befehls für die Parameter in der Ausgabe.

### <a name="examples"></a>Beispiele

**Erstellen einer Sammlung ohne Shards**

Verwenden Sie den folgenden Befehl, um eine Sammlung ohne Shards mit dem Namen „testCollection“ und einem bereitgestellten Durchsatz von 1.000 RUs zu erstellen: 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**Erstellen einer Sammlung mit Shards**

Verwenden Sie den folgenden Befehl, um eine Sammlung mit Shards mit dem Namen „testCollection“ und einem bereitgestellten Durchsatz von 1.000 RUs zu erstellen:

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a id="update-collection"></a> Aktualisieren der Sammlung

Mit dem Erweiterungsbefehl „UpdateCollection“ werden die Eigenschaften aktualisiert, die der angegebenen Sammlung zugeordnet sind.

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

In der folgenden Tabelle sind die Parameter des Befehls beschrieben:

|**Feld**|**Typ** |**Beschreibung** |
|---------|---------|---------|
|  customAction   |   Zeichenfolge      |   Der Name des benutzerdefinierten Befehls. Er muss „UpdateCollection“ lauten.      |
|  collection   |   Zeichenfolge      |   Name der Sammlung       |
| offerThroughput   |int|   Bereitgestellter Durchsatz, der für die Sammlung festgelegt wird.|

## <a name="output"></a>Output

Gibt eine Standardantwort für den benutzerdefinierten Befehl zurück. Informationen hierzu finden Sie in der [Standardausgabe](#default-output) des benutzerdefinierten Befehls für die Parameter in der Ausgabe.

### <a name="examples"></a>Beispiele

**Aktualisieren des bereitgestellten Durchsatzes für eine Sammlung**

Verwenden Sie den folgenden Befehl, um den bereitgestellten Durchsatz einer Sammlung mit dem Namen „testCollection“ auf 1.200 RUs zu aktualisieren:

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a id="get-collection"></a> Abrufen der Sammlung

Mit dem benutzerdefinierten Befehl „GetCollection“ wird das Sammlungsobjekt zurückgegeben.

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

In der folgenden Tabelle sind die Parameter des Befehls beschrieben:


|**Feld**|**Typ** |**Beschreibung** |
|---------|---------|---------|
| customAction    |   Zeichenfolge      |   Der Name des benutzerdefinierten Befehls. Er muss „GetCollection“ lauten.      |
| collection    |    Zeichenfolge     |    Name der Sammlung     |

### <a name="output"></a>Output

Wenn der Befehl erfolgreich ist, enthält die Antwort ein Dokument mit den folgenden Feldern:


|**Feld**|**Typ** |**Beschreibung** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Status der Antwort. 1 == Erfolgreich. 0 == Fehler.      |
| `database`    |    `string`     |   Der Name der Datenbank.      |
| `collection`    |    `string`     |    Name der Sammlung     |
|  `shardKeyDefinition`   |   `document`      |  Dokument mit Indexspezifikation zur Verwendung als Shardschlüssel. Dies ist ein optionaler Antwortparameter.       |
|  `provisionedThroughput`   |   `int`      |    Bereitgestellter Durchsatz, der für die Sammlung festgelegt werden soll. Dies ist ein optionaler Antwortparameter.     |

Wenn der Befehl nicht erfolgreich ist, wird eine Standardantwort für den benutzerdefinierten Befehl zurückgegeben. Informationen hierzu finden Sie in der [Standardausgabe](#default-output) des benutzerdefinierten Befehls für die Parameter in der Ausgabe.

### <a name="examples"></a>Beispiele

**Abrufen der Sammlung**

Verwenden Sie den folgenden Befehl, um das Sammlungsobjekt für eine Sammlung mit dem Namen „testCollection“ abzurufen:

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a id="default-output"></a> Standardausgabe eines benutzerdefinierten Befehls

Wenn nichts anderes angegeben ist, enthält eine benutzerdefinierte Antwort ein Dokument mit den folgenden Feldern:

|**Feld**|**Typ** |**Beschreibung** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Status der Antwort. 1 == Erfolgreich. 0 == Fehler.      |
| `code`    |   `int`      |   Wird nur zurückgegeben, wenn der Befehl nicht erfolgreich ist (ok == 0). Enthält den MongoDB-Fehlercode. Dies ist ein optionaler Antwortparameter.      |
|  `errMsg`   |  `string`      |    Wird nur zurückgegeben, wenn der Befehl nicht erfolgreich ist (ok == 0). Enthält eine benutzerfreundliche Fehlermeldung. Dies ist ein optionaler Antwortparameter.      |

## <a name="next-steps"></a>Nächste Schritte

Sie können sich nun über die folgenden Azure Cosmos DB-Konzepte informieren: 

* [Indizierung in Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Festlegen einer Gültigkeitsdauer für den automatischen Ablauf von Daten in Azure Cosmos DB](../cosmos-db/time-to-live.md)
