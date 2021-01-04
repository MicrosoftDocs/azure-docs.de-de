---
title: 'Migrieren Ihrer Daten zu einem Cassandra-API-Konto in Azure Cosmos DB: Tutorial'
description: Hier erfahren Sie, wie Sie den CQL-Kopierbefehl und Spark verwenden, um Daten aus Apache Cassandra in ein Cassandra-API-Konto in Azure Cosmos DB zu kopieren.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: bd2d27addb6860e49ac12eb36d8b625b8bf92001
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100404"
---
# <a name="tutorial-migrate-your-data-to-cassandra-api-account-in-azure-cosmos-db"></a>Tutorial: Migrieren Ihrer Daten zu einem Cassandra-API-Konto in Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Entwickler verfügen unter Umständen über lokal oder in der Cloud ausgeführte Cassandra-Workloads, die sie zu Azure migrieren möchten. Solche Workloads können zu einem Cassandra-API-Konto in Azure Cosmos DB migriert werden. Dieses Tutorial enthält Informationen zu verschiedenen Optionen, die für die Migration von Apache Cassandra-Daten zum Cassandra-API-Konto in Azure Cosmos DB zur Verfügung stehen.

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Planen der Migration
> * Voraussetzungen für die Migration
> * Migrieren von Daten mithilfe des cqlsh-Befehls COPY
> * Migrieren von Daten mithilfe von Spark

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites-for-migration"></a>Voraussetzungen für die Migration

* **Ermitteln des voraussichtlichen Durchsatzbedarfs:** Ermitteln Sie den voraussichtlichen Durchsatzbedarf für Ihre Workload, bevor Sie Daten zum Cassandra-API-Konto in Azure Cosmos DB migrieren. Im Allgemeinen wird für den Einstieg der für CRUD-Vorgänge erforderliche durchschnittliche Durchsatz empfohlen. Fügen Sie später den zusätzlich für ETL (Extrahieren, Transformieren und Laden) oder für Vorgänge mit Lastspitzen erforderlichen Durchsatz hinzu. Sie benötigen die folgenden Details für die Planung der Migration: 

  * **Größe der vorhandenen Daten oder geschätzte Datengröße:** Legen Sie die Mindestanforderungen für die Datenbankgröße und den Durchsatz fest. Wenn Sie die Datengröße für eine neue Anwendung schätzen, können Sie davon ausgehen, dass die Daten gleichmäßig über die Zeilen verteilt sind und den Wert durch Multiplizieren mit der Datengröße schätzen. 

  * **Erforderlicher Durchsatz:** Geben Sie die ungefähre Durchsatzrate für Lesen (Abfragen/Abrufen) und Schreiben (Aktualisieren/Löschen/Einfügen) an. Dieser Wert ist erforderlich, um die erforderlichen Anforderungseinheiten zusammen mit der Größe der Daten mit festem Zustand zu berechnen.  

  * **Das Schema:** Stellen Sie über cqlsh eine Verbindung mit Ihrem Cassandra-Cluster her, und exportieren Sie das Schema aus Cassandra: 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    Nachdem Sie die Anforderungen Ihrer Workload ermittelt haben, sollten Sie anhand der gesammelten Durchsatzanforderungen ein Azure Cosmos-Konto, eine Datenbank und Container erstellen.  

  * **Bestimmen der RU-Gebühr für einen Vorgang:** Sie können die RUs mithilfe eines der von der Cassandra-API unterstützten SDKs ermitteln. Dieses Beispiel zeigt die .NET-Version zum Abrufen von RU-Gebühren.

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

* **Zuordnen des erforderlichen Durchsatzes:** Azure Cosmos DB kann Speicher und Durchsatz automatisch gemäß Ihren wachsenden Anforderungen skalieren. Sie können Ihre Durchsatzanforderungen mit dem [Anforderungseinheitenrechner von Azure Cosmos DB](https://www.documentdb.com/capacityplanner) schätzen. 

* **Erstellen von Tabellen im Cassandra-API-Konto:** Bevor Sie mit der Migration von Daten beginnen, erstellen Sie zunächst alle Ihre Tabellen im Azure-Portal oder mit cqlsh. Wenn Sie als Migrationsziel ein Azure Cosmos-Konto mit Durchsatz auf Datenbankebene verwenden, geben Sie beim Erstellen der Azure Cosmos-Container einen Partitionsschlüssel an.

* **Erhöhung des Durchsatzes:** Die Dauer der Datenmigration richtet sich nach der Durchsatzmenge, die Sie für Ihre Tabellen in Azure Cosmos DB bereitstellen. Erhöhen Sie den Durchsatz für die Dauer der Migration. Mit dem höheren Durchsatz können Sie eine Ratenbegrenzung vermeiden und Migrationen in kürzerer Zeit durchführen. Nachdem die Migration abgeschlossen ist, können Sie den Durchsatz wieder verringern, um Kosten zu sparen. Das Azure Cosmos-Konto sollte sich außerdem in der gleichen Region befinden wie Ihre Quelldatenbank. 

* **Aktivieren von TLS:** Für Azure Cosmos DB gelten strenge Sicherheitsanforderungen und -standards. Achten Sie darauf, TLS für die Interaktion mit Ihrem Konto zu aktivieren. Wenn Sie CQL mit SSH verwenden, können Sie TLS-Informationen bereitstellen.

## <a name="options-to-migrate-data"></a>Optionen zum Migrieren von Daten

Mithilfe der folgenden Optionen können Sie Daten aus vorhandenen Cassandra-Workloads nach Azure Cosmos DB verschieben:

* [Mithilfe des cqlsh-Befehls COPY](#migrate-data-using-cqlsh-copy-command)  
* [Mit Spark](#migrate-data-using-spark) 

## <a name="migrate-data-using-cqlsh-copy-command"></a>Migrieren von Daten mithilfe des cqlsh-Befehls COPY

Der [CQL-Befehl COPY](https://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) wird verwendet, um lokale Daten in das Cassandra-API-Konto in Azure Cosmos DB zu kopieren. Führen Sie zum Kopieren von Daten die folgenden Schritte aus:

1. Rufen Sie die Informationen zur Verbindungszeichenfolge für das Cassandra-API-Konto ab:

   * Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem Azure Cosmos-Konto.

   * Öffnen Sie den Bereich **Verbindungszeichenfolge**. Sie finden dort alle Informationen, die Sie benötigen, um Ihr Cassandra-API-Konto per cqlsh zu verbinden.

2. Melden Sie sich bei cqlsh mit den Verbindungsinformationen aus dem Portal an.

3. Kopieren Sie mit dem CQL-Befehl COPY lokale Daten zum Cassandra API-Konto.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-using-spark"></a>Migrieren von Daten mithilfe von Spark 

Führen Sie die folgenden Schritte aus, um Daten mithilfe von Spark zum Cassandra-API-Konto zu migrieren:

- Stellen Sie einen [Azure Databricks-Cluster](cassandra-spark-databricks.md) oder einen [HDInsight-Cluster](cassandra-spark-hdinsight.md) bereit. 

- Verschieben Sie mithilfe des [Tabellenkopiervorgangs](cassandra-spark-table-copy-ops.md) Daten an den Cassandra-API-Zielendpunkt. 

Das Migrieren von Daten mithilfe von Spark-Aufträgen empfiehlt sich, wenn sich Ihre Daten in einem vorhandenen Cluster auf virtuellen Azure-Computern oder in einer anderen Cloud befinden. Bei dieser Option muss Spark für die einmalige oder regelmäßige Erfassung zwischengeschaltet werden. Sie können diese Migration mithilfe von Azure ExpressRoute-Verbindungen zwischen der lokalen Umgebung und Azure beschleunigen. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcengruppe, das Azure Cosmos-Konto und die dazugehörigen Ressourcen nicht mehr benötigen, können Sie sie löschen. Wählen Sie dazu die Ressourcengruppe für den virtuellen Computer und anschließend **Löschen** aus, und bestätigen Sie den Namen der zu löschenden Ressourcengruppe.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie Ihre Daten zu einem Cassandra-API-Konto in Azure Cosmos DB migrieren. Im nächsten Artikel finden Sie Informationen zu weiteren Azure Cosmos DB-Konzepten:

> [!div class="nextstepaction"]
> [Einstellbare Datenkonsistenzebenen in Azure Cosmos DB](../cosmos-db/consistency-levels.md)


