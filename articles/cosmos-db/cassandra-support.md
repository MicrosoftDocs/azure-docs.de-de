---
title: Apache Cassandra-Features, die von der Cassandra-API für Azure Cosmos DB unterstützt werden
description: Informationen zur Unterstützung der Apache Cassandra-Features in der Cassandra-API für Azure Cosmos DB
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 8c51450fb6ce5c381784e6aaf9b1a66c3c4ff153
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188546"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Apache Cassandra-Features, die von der Cassandra-API für Azure Cosmos DB unterstützt werden 
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB ist ein global verteilter Datenbankdienst von Microsoft mit mehreren Modellen. Sie können mit der Cassandra-API von Azure Cosmos DB über Open-Source-[Clienttreiber](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) für Cassandra kommunizieren, die mit der Version 4 des binären CQL-[Wire-Protokolls](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) kompatibel sind. 

Durch Verwendung der Cassandra-API für Azure Cosmos DB können Sie die Vorteile der Apache Cassandra-APIs sowie die von Azure Cosmos DB gebotenen Unternehmensfunktionen nutzen. Die Unternehmensfunktionen umfassen [globale Verteilung](distribute-data-globally.md), [automatische Partitionierung mit Aufskalieren](cassandra-partitioning.md), Gewährleistung der Verfügbarkeit und Latenz, Verschlüsselung bei REST, Sicherungen und vieles mehr.

## <a name="cassandra-protocol"></a>Cassandra-Protokoll 

Die Cassandra-API für Azure Cosmos DB ist mit der CQL-API (Cassandra Query Language), Version 3.11 kompatibel und abwärtskompatibel mit Version 2.x. Die unterstützten CQL-Befehle, Tools, Einschränkungen und Ausnahmen sind nachstehend aufgeführt. Alle Clienttreiber, die diese Protokolle verstehen, sollten auch zur Cassandra-API für Azure Cosmos DB eine Verbindung herstellen können.

## <a name="cassandra-driver"></a>Cassandra-Treiber

Die folgenden Versionen von Cassandra-Treibern werden von der Cassandra-API für Azure Cosmos DB unterstützt:

* [Java 3.5+](https://github.com/datastax/java-driver)  
* [C# 3.5+](https://github.com/datastax/csharp-driver)  
* [Nodejs 3.5+](https://github.com/datastax/nodejs-driver)  
* [Python 3.15+](https://github.com/datastax/python-driver)  
* [C++ 2.9](https://github.com/datastax/cpp-driver)   
* [PHP 1.3](https://github.com/datastax/php-driver)  
* [Gocql](https://github.com/gocql/gocql)  
 

## <a name="cql-data-types"></a>CQL-Datentypen 

Die Cassandra-API für Azure Cosmos DB unterstützt die folgenden CQL-Datentypen:

|Get-Help  |Unterstützt |
|---------|---------|
| ascii  | Ja |
| BIGINT  | Ja |
| Blob  | Ja |
| boolean  | Ja |
| Zähler  | Ja |
| date  | Ja |
| Decimal  | Ja |
| double  | Ja |
| float  | Ja |
| frozen  | Ja |
| inet  | Ja |
| INT  | Ja |
| list  | Ja |
| set  | Ja |
| SMALLINT  | Ja |
| text  | Ja |
| time  | Ja |
| timestamp  | Ja |
| timeuuid  | Ja |
| TINYINT  | Ja |
| tuple  | Ja |
| uuid  | Ja |
| varchar  | Ja |
| varint  | Ja |
| tuples | Ja | 
| udts  | Ja |
| map | Ja |

„Static“ wird für die Datentypdeklaration unterstützt.

## <a name="cql-functions"></a>CQL-Funktionen

Die Cassandra-API für Azure Cosmos DB unterstützt die folgenden CQL-Funktionen:

|Get-Help  |Unterstützt |
|---------|---------|
| Token * | Ja |
| ttl | Ja |
| writetime | Ja |
| Umwandlung | Nein |

> [!NOTE]
> \* Die Cassandra-API unterstützt Token als Projektion/Selektor. Außerdem ist die Verwendung von token(pk) nur auf der linken Seite einer WHERE-Klausel zulässig. Beispiel: `WHERE token(pk) > 1024` wird unterstützt, aber `WHERE token(pk) > token(100)` wird **nicht** unterstützt.



Aggregatfunktionen:

|Get-Help  |Unterstützt |
|---------|---------|
| avg | Ja |
| count | Ja |
| Min. | Ja |
| max | Ja |
| Sum | Ja |

> [!NOTE]
> Aggregatfunktionen können für reguläre Spalten verwendet werden. Aggregate für Clusteringspalten werden jedoch **nicht** unterstützt.


Blob-Konvertierungsfunktionen:
 
|Get-Help  |Unterstützt |
|---------|---------|
| typeAsBlob(value)   | Ja |
| blobAsType(value) | Ja |


UUID- und timeuuid-Funktionen:
 
|Get-Help  |Unterstützt |
|---------|---------|
| dateOf()  | Ja |
| now()  | Ja |
| minTimeuuid()  | Ja |
| unixTimestampOf()  | Ja |
| toDate(timeuuid)  | Ja |
| toTimestamp(timeuuid)  | Ja |
| toUnixTimestamp(timeuuid)  | Ja |
| toDate(timestamp)  | Ja |
| toUnixTimestamp(timestamp)  | Ja |
| toTimestamp(date)  | Ja |
| toUnixTimestamp(date) | Ja |


  
## <a name="cql-commands"></a>CQL-Befehle

Azure Cosmos DB unterstützt die folgenden Datenbankbefehle für Cassandra-API-Konten.

|Get-Help  |Unterstützt |
|---------|---------|
| ALLOW FILTERING | Ja |
| ALTER KEYSPACE | Nicht verfügbar (PaaS-Dienst, Replikation wird intern verwaltet)|
| ALTER MATERIALIZED VIEW | Nein |
| ALTER ROLE | Nein |
| ALTER TABLE | Ja |
| ALTER TYPE | Nein |
| ALTER USER | Nein |
| BATCH | Ja (Nur nicht protokollierter Batch)|
| COMPACT STORAGE | Nicht verfügbar (PaaS-Dienst) |
| CREATE AGGREGATE | Nein | 
| CREATE CUSTOM INDEX (SASI) | Nein |
| CREATE INDEX | Ja (ohne [Angabe eines Indexnamens](cassandra-secondary-index.md); Indizes auf Clusterschlüsseln oder vollständige FROZEN-Sammlung werden nicht unterstützt) |
| CREATE FUNCTION | Nein |
| CREATE KEYSPACE (Replikationseinstellungen werden ignoriert) | Ja |
| CREATE MATERIALIZED VIEW | Nein |
| CREATE TABLE | Ja |
| CREATE TRIGGER | Nein |
| CREATE TYPE | Ja |
| CREATE ROLE | Nein |
| CREATE USER (in nativer Apache Cassandra-Version veraltet) | Nein |
| Delete | Ja |
| DELETE (einfache Transaktionen mit IF-Bedingung)| Ja |
| DISTINCT | Nein  |
| DROP AGGREGATE | Nein |
| DROP FUNCTION | Nein |
| DROP INDEX | Ja |
| DROP KEYSPACE | Ja |
| DROP MATERIALIZED VIEW | Nein |
| DROP ROLE | Nein |
| DROP TABLE | Ja |
| DROP TRIGGER | Nein | 
| DROP TYPE | Ja |
| DROP USER (in nativer Apache Cassandra-Version veraltet) | Nein |
| GRANT | Nein |
| INSERT | Ja |
| INSERT (einfache Transaktionen mit IF-Bedingung)| Ja |
| LIST PERMISSIONS | Nein |
| LIST ROLES | Nein |
| LIST USERS (in nativer Apache Cassandra-Version veraltet) | Nein |
| REVOKE | Nein |
| SELECT | Ja |
| SELECT (einfache Transaktionen mit IF-Bedingung)| Nein |
| UPDATE | Ja |
| UPDATE (einfache Transaktionen mit IF-Bedingung)| Nein |
| TRUNCATE | Nein |
| USE | Ja |

## <a name="json-support"></a>JSON-Unterstützung
|Get-Help  |Unterstützt |
|---------|---------|
| SELECT JSON | Ja |
| INSERT JSON | Ja |
| fromJson() | Nein |
| toJson() | Nein |


## <a name="cassandra-api-limits"></a>Einschränkungen für die Cassandra-API

Bei der Cassandra-API für Azure Cosmos DB gibt es keine Einschränkungen hinsichtlich der Größe von Daten, die in einer Tabelle gespeichert sind. Es können Hunderte von Terabytes oder Petabytes von Daten gespeichert werden. Gleichzeitig wird sichergestellt, dass die Grenzwerte für Partitionsschlüssel berücksichtigt werden. In ähnlicher Weise gibt es bei jedem Entitäts- oder Zeilenäquivalent keine Einschränkungen hinsichtlich der Anzahl von Spalten. Die Gesamtgröße der Entität sollte jedoch 2 MB nicht überschreiten. Die Datenmenge für jeden Partitionsschlüssel darf wie bei allen anderen APIs auch 20 GB nicht überschreiten.

## <a name="tools"></a>Tools 

Die Cassandra-API für Azure Cosmos DB ist eine verwaltete Dienstplattform. Sie erfordert keinen Verwaltungsaufwand oder Dienstprogramme wie Garbage Collector, Java Virtual Machine (JVM) und Nodetool zum Verwalten des Clusters. Sie unterstützt Tools wie cqlsh, das die binäre CQLv4-Kompatibilität nutzt. 

* Der Daten-Explorer des Azure-Portals, Metriken, Protokolldiagnose, PowerShell und CLI sind weitere unterstützte Mechanismen zur Verwaltung des Kontos.

## <a name="hosted-cql-shell-preview"></a>Gehostete CQL-Shell (Vorschauversion)

Sie können eine gehostete native Cassandra-Shell (CQLSH v5.0.1) direkt über den Daten-Explorer im [Azure-Portal](data-explorer.md) oder über den [Azure Cosmos DB-Explorer](https://cosmos.azure.com/) öffnen. Vor dem Aktivieren der CQL-Shell müssen Sie [das Feature „Notebooks“ in Ihrem Konto aktivieren](enable-notebooks.md). (Sollte es noch nicht aktiviert sein, werden Sie beim Klicken auf `Open Cassandra Shell` dazu aufgefordert.) Informationen zu unterstützten Azure-Regionen finden Sie im hervorgehobenen Hinweis des Artikels [Aktivieren von Notebooks für Azure Cosmos DB-Konten (Vorschau)](enable-notebooks.md).

:::image type="content" source="./media/cassandra-support/cqlsh.png" alt-text="Öffnen von CQLSH":::

Sie können auch in Azure Cosmos DB eine Verbindung mit der Cassandra-API herstellen, indem Sie die auf einem lokalen Computer installierte CQLSH-Instanz verwenden. Sie enthält Apache Cassandra 3.1.1 und kann durch Festlegen der Umgebungsvariablen sofort verwendet werden. In den folgenden Abschnitten finden Sie Anleitungen zur Installation, Konfiguration und Verbindungsherstellung mit der Cassandra-API in Azure Cosmos DB unter Windows oder Linux mit CQLSH.

> [!NOTE]
> Verbindungen zur Cassandra-API von Azure Cosmos DB sind mit DSE-Versionen (DataStax Enterprise) von CQLSH nicht erfolgreich. Achten Sie darauf, nur die Open-Source-Versionen von CQLSH für Apache Cassandra zu verwenden, wenn Sie eine Verbindung zur Cassandra-API herstellen. 

**Windows:**

Wenn Sie Windows verwenden, empfiehlt es sich, das [Windows-Dateisystem für Linux](/windows/wsl/install-win10#install-the-windows-subsystem-for-linux) zu aktivieren. Anschließend können Sie die folgenden Linux-Befehle verwenden.

**Unix/Linux/Mac:**

```bash
# Install default-jre and default-jdk
sudo apt install default-jre
sudo apt-get update
sudo apt install default-jdk

# Import the Baltimore CyberTrust root certificate:
curl https://cacert.omniroot.com/bc2025.crt > bc2025.crt
keytool -importcert -alias bc2025ca -file bc2025.crt

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to Azure Cosmos DB API for Cassandra:
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl

```

Alle CRUD-Vorgänge geben bei Ausführung über ein CQL v4-kompatibles SDK zusätzliche Informationen zu Fehlern und beanspruchten Anforderungseinheiten zurück. Bei der Verarbeitung der Befehle DELETE und UPDATE sollte die Ressourcenkontrolle in Erwägung gezogen werden, um die effizienteste Verwendung des bereitgestellten Durchsatzes sicherzustellen.

* Hinweis: Der Wert „gc_grace_seconds“ muss Null sein, wenn er angegeben wird.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($"CustomPayload:  {key}: {value}"); 
        } 
```

## <a name="consistency-mapping"></a>Konsistenzzuordnung 

Die Cassandra-API für Azure Cosmos DB ermöglicht die Wahl der Konsistenz bei Lesevorgängen.  Ausführliche Informationen zur Konsistenzzuordnung finden Sie [hier](./cassandra-consistency.md#mapping-consistency-levels).

## <a name="permission-and-role-management"></a>Berechtigungs- und Rollenverwaltung

Azure Cosmos DB unterstützt die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) für die Bereitstellung, Rotation von Schlüsseln, Anzeige von Metriken sowie Lese-/Schreibkennwörter bzw. Lese-/Schreibschlüssel und Schreibschutzkennwörter/-schlüssel, die über das [Azure-Portal](https://portal.azure.com) abgerufen werden können. Azure Cosmos DB unterstützt keine Rollen für CRUD-Aktivitäten.

## <a name="keyspace-and-table-options"></a>Keyspace- und Tabellenoptionen

Die Optionen für Regionsname, Klasse, Replikationsfaktor und Rechenzentrum im Befehl „CREATE KEYSPACE“ werden derzeit ignoriert. Das System verwendet die Replikationsmethode zur [globalen Datenverteilung](global-dist-under-the-hood.md) der zugrunde liegenden Azure Cosmos DB-Instanz, um die Regionen hinzuzufügen. Wenn die Daten regionsübergreifend vorhanden sein müssen, können Sie dies mithilfe von PowerShell, per CLI oder über das Portal auf der Kontoebene aktivieren. Weitere Informationen finden Sie unter [Hinzufügen/Entfernen von Regionen für Ihr Datenbankkonto](how-to-manage-database-account.md#addremove-regions-from-your-database-account). „Durable_writes“ kann nicht deaktiviert werden, da Azure Cosmos DB sicherstellt, dass jeder Schreibvorgang dauerhaft ist. In jeder Region repliziert Azure Cosmos DB die Daten innerhalb der gesamten Replikatgruppe, die sich aus vier Replikaten zusammensetzt. Diese [Konfiguration](global-dist-under-the-hood.md) der Replikatgruppe kann nicht geändert werden.
 
Beim Erstellen der Tabelle werden mit Ausnahme von „gc_grace_seconds“ alle Optionen ignoriert, und „gc_grace_seconds“ muss auf Null festgelegt werden.
Für den Keyspace und die Tabelle steht eine zusätzliche Option namens „cosmosdb_provisioned_throughput“ mit einem Mindestwert von 400 RU/s zur Verfügung. Der Keyspacedurchsatz ermöglicht die tabellenübergreifende gemeinsame Nutzung des Durchsatzes und ist hilfreich in Szenarien, in denen alle Tabellen nicht den bereitgestellten Durchsatz beanspruchen. Mit dem Befehl „ALTER TABLE“ kann der bereitgestellte Durchsatz regionsübergreifend geändert werden. 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```
## <a name="secondary-index"></a>Sekundärer Index
Die Cassandra-API unterstützt sekundäre Indizes für alle Datentypen außer fixierten Sammlungstypen, Dezimal- und Variant-Typen. 

## <a name="usage-of-cassandra-retry-connection-policy"></a>Verwendung der Cassandra-Richtlinie für die Verbindungswiederholung

Azure Cosmos DB ist ein ressourcengesteuertes System. Das bedeutet, dass pro Sekunde eine bestimmte Anzahl von Vorgängen ausgeführt werden kann – abhängig von den Anforderungseinheiten, die durch die Vorgänge beansprucht werden. Überschreitet eine Anwendung dieses Limit in einer Sekunde, wird die Anforderungsrate begrenzt, und Ausnahmen werden ausgelöst. Diese Ausnahmen werden von der Cassandra-API in Azure Cosmos DB in Überladungsfehler im nativen Cassandra-Protokoll übersetzt. Um sicherzustellen, dass Ihre Anwendung im Falle einer Ratenbegrenzung Anforderungen abfangen und wiederholen kann, werden die Erweiterungen für [Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) und [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) bereitgestellt. Sehen Sie sich auch Java-Codebeispiele für Datastax-Treiber der [Version 3](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) und [Version 4](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4) an, wenn Sie eine Verbindung mit der Cassandra-API in Azure Cosmos DB herstellen. Wenn Sie über andere SDKs auf die Cassandra-API in Azure Cosmos DB zugreifen, müssen Sie eine Verbindungsrichtlinie erstellen, um für diese Ausnahmen eine Wiederholung durchzuführen.

## <a name="next-steps"></a>Nächste Schritte

- Erste Schritte mit dem [Erstellen eines Cassandra-API-Kontos, einer Datenbank und einer Tabelle](create-cassandra-api-account-java.md) mithilfe einer Java-Anwendung
