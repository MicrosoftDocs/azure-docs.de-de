---
title: Migrieren von Daten zur Azure Cosmos DB-Cassandra-API mithilfe von Blitzz
description: Erfahren Sie, wie Sie Daten aus einer Oracle-Datenbank zur Azure Cosmos DB-Cassandra-API mithilfe von Blitzz migrieren.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 15bcd0c54fc5f6614f4d1925759704309048acae
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336439"
---
# <a name="migrate-data-from-oracle-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>Migrieren von Daten aus Oracle zum Azure Cosmos DB-Cassandra-API-Konto mithilfe von Blitzz
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Die Cassandra-API in Azure Cosmos DB hat sich aus einer Reihe unterschiedlicher Gründe zu einer guten Wahl für Unternehmensworkloads entwickelt, die unter Oracle ausgeführt werden:

* **Bessere Skalierbarkeit und Verfügbarkeit:** Beseitigt einzelne Fehlerquellen, verbessert die Skalierbarkeit und Verfügbarkeit für Ihre Anwendungen.

* **Erhebliche Kosteneinsparungen:** Sie können Kosten sparen mit Azure Cosmos DB, einschließlich der Kosten für virtuelle Computer, Bandbreite und alle anwendbaren Oracle-Lizenzen. Außerdem entfällt die Verwaltung der Kosten für Rechenzentren, Server, SSD-Speicher, Netzwerk und Strom.

* **Kein Mehraufwand für Verwaltung und Überwachung:** Als vollständig verwalteter Clouddienst entfernt Azure Cosmos DB den Mehraufwand der Verwaltung und Überwachung von unzähligen Einstellungen.

Es gibt verschiedene Möglichkeiten, Datenbankworkloads von einer Plattform zu einer anderen zu migrieren. [Blitzz](https://www.blitzz.io) ist ein Tool, das eine sichere und zuverlässige Möglichkeit bietet, eine Migration verschiedener Datenbanken zu Azure Cosmos DB ohne Ausfallzeiten durchzuführen. In diesem Artikel werden die erforderlichen Schritte beschrieben, mit denen Sie Daten aus einer Oracle-Datenbank zur Azure Cosmos DB-Cassandra-API mithilfe von Blitzz migrieren.

## <a name="benefits-using-blitzz-for-migration"></a>Vorteile der Verwendung von Blitzz für die Migration

Die Migrationslösung von Blitzz befolgt einen schrittweisen Ansatz zum Migrieren komplexer Betriebsworkloads. Im Folgenden finden Sie einige der wichtigsten Aspekte des Migrationsplans ohne Ausfallzeiten von Blitzz:

* Er bietet die automatische Migration der Geschäftslogik (Tabellen, Indizes, Sichten) aus einer Oracle-Datenbank zu Azure Cosmos DB. Sie müssen keine Schemas manuell erstellen.

* Blitzz bietet eine parallele Datenbankreplikation für hohe Volumen. Es ermöglicht sowohl den Quell- als auch den Zielplattformen, während der Migration synchron zu bleiben, durch Einsatz eines Verfahrens namens Change-Data-Capture (CDC). Mithilfe von CDC ruft Blitzz kontinuierlich einen Datenstrom mit Änderungen aus der Quelldatenbank (Oracle) ab und wendet ihn auf die Zieldatenbank (Azure Cosmos DB) an.

* Dies ist fehlertolerant und garantiert eine genau einmalige Übermittlung von Daten, selbst während eines Hardware- oder Softwarefehlers im System.

* Die Daten werden während der Übertragung mithilfe verschiedener Sicherheitsmethoden wie TLS/SSL oder Verschlüsselung gesichert.

* Er bietet Dienste zum Konvertieren komplexer Geschäftslogiken, die in PL/SQL geschrieben sind, in entsprechende Geschäftslogiken in Azure Cosmos DB.

## <a name="steps-to-migrate-data"></a>Schritte zum Migrieren von Daten

In diesem Abschnitt werden die erforderlichen Schritte zum Einrichten von Blitzz und zum Migrieren von Daten aus einer Oracle-Datenbank zu Azure Cosmos DB beschrieben.

1. Fügen Sie von dem Computer, auf dem Sie den Blitzz-Replikanten installieren möchten, ein Sicherheitszertifikat hinzu. Dieses Zertifikat wird von dem Blitzz-Replikanten benötigt, um eine TLS-Verbindung mit dem angegebenen Azure Cosmos DB-Konto herzustellen. Sie können das Zertifikat mit den folgenden Schritten hinzufügen:

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. Sie können die Blitzz-Installation sowie die Binärdateien entweder durch Anforderung einer Demo auf der [Blitzz-Website](https://www.blitzz.io) erhalten. Oder Sie können alternativ auch eine [E-Mail](mailto:success@blitzz.io) an das Team senden.

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/blitzz-replicant-download.png" alt-text="Download des Blitzz-Replikantentools":::

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/replicant-files.png" alt-text="Blitzz-Replikantendateien":::

1. Richten Sie im CLI-Terminal die Konfiguration der Quelldatenbank ein. Öffnen Sie die Konfigurationsdatei mithilfe des Befehls **`vi conf/conn/oracle.yml`** , und fügen Sie eine durch Trennzeichen getrennte Liste mit den IP-Adressen der Oracle-Knoten, die Portnummer, den Benutzernamen, das Kennwort und alle anderen erforderlichen Details hinzu. Der folgende Code zeigt eine Beispielkonfigurationsdatei:

   ```bash
   type: ORACLE

   host: localhost
   port: 53546

   service-name: IO

   username: '<Username of your Oracle database>'
   password: '<Password of your Oracle database>'

   conn-cnt: 30
   use-ssl: false
   ```

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/open-connection-editor-oracle.png" alt-text="Öffnen des Oracle-Verbindungs-Editors":::

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/oracle-connection-configuration.png" alt-text="Oracle-Verbindungskonfiguration":::

   Nachdem Sie die Konfigurationsdetails ausgefüllt haben, speichern und schließen Sie die Datei.

1. Optional können Sie die Filterdatei der Quelldatenbank einrichten. Die Filterdatei gibt an, welche Schemas oder Tabellen migriert werden sollen. Öffnen Sie die Konfigurationsdatei, indem Sie den Befehl **`vi filter/oracle_filter.yml`** verwenden und die folgenden Konfigurationsdetails eingeben:

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```
 
   Nachdem Sie die Datenbankfilterdetails ausgefüllt haben, speichern und schließen Sie die Datei.

1. Als Nächstes richten Sie die Konfiguration der Zieldatenbank ein. Bevor Sie die Konfiguration definieren, [erstellen Sie ein Azure Cosmos DB-Cassandra-API-Konto](create-cassandra-dotnet.md#create-a-database-account). [Wählen Sie den richtigen Partitionsschlüssel](partitioning-overview.md#choose-partitionkey) aus Ihren Daten aus, und erstellen Sie dann einen Keyspace sowie eine Tabelle, um die migrierten Daten zu speichern.

1. Erhöhen Sie vor dem Migrieren der Daten den Containerdurchsatz auf den Betrag, der für eine schnelle Migration Ihrer Anwendung erforderlich ist. Beispielsweise können Sie den Durchsatz auf 100.000 RUs erhöhen. Das Skalieren des Durchsatzes vor dem Starten der Migration hilft Ihnen, Ihre Daten in kürzerer Zeit zu migrieren. 

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/scale-throughput.png" alt-text="Skalieren des Azure Cosmos-Containerdurchsatzes":::

   Nachdem die Migration abgeschlossen ist, müssen Sie den Durchsatz wieder verringern. Basierend auf der Menge der gespeicherten Daten und der erforderlichen RUs für jeden Vorgang können Sie den nach der Datenmigration erforderlichen Durchsatz abschätzen. Weitere Informationen zum Abschätzen der erforderlichen RUs finden Sie in den Artikeln [Bereitstellen von Durchsatz für Container und Datenbanken](set-throughput.md) und [Schätzen des Durchsatzes (RU/s) mit dem Azure Cosmos DB Capacity Planner](estimate-ru-with-capacity-planner.md).

1. Besorgen Sie sich den **Kontaktpunkt, Port, Benutzernamen** und das **primäre Kennwort** Ihres Azure Cosmos-Kontos aus dem Bereich **Verbindungszeichenfolge**. Diese Werte werden in der Konfigurationsdatei verwendet.

1. Richten Sie im CLI-Terminal die Konfiguration der Zieldatenbank ein. Öffnen Sie die Konfigurationsdatei mit dem Befehl **`vi conf/conn/cosmosdb.yml`** , und fügen Sie eine durch Trennzeichen getrennte Liste von Host-URI, Portnummer, Benutzername, Kennwort und anderen erforderlichen Parametern hinzu. Im Folgenden finden Sie ein Beispiel für die Inhalte der Konfigurationsdatei:

   ```bash
   type: COSMOSDB

   host: `<Azure Cosmos account’s Contact point>`
   port: 10350

   username: 'blitzzdemo'
   password: `<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   use-ssl: false
   ```

1. Als Nächstes migrieren Sie die Daten mithilfe von Blitzz. Sie können den Blitzz-Replikanten im **vollständigen** oder **Momentaufnahme** modus ausführen:

   * **Vollständiger Modus** : In diesem Modus wird der Replikant nach der Migration weiter ausgeführt und lauscht auf Änderungen am Oracle-Quellsystem. Wenn es Änderungen erkennt, werden Sie in Echtzeit in das Azure Cosmos-Zielkonto repliziert.

   * **Momentaufnahmemodus** : In diesem Modus können Sie eine Schemamigration sowie eine einmalige Datenreplikation durchführen. Echtzeitreplikation wird mit dieser Option nicht unterstützt.


   Durch Verwendung der beiden oben genannten Modi kann die Migration ohne jegliche Ausfallzeiten durchgeführt werden.

1. Um Daten zu migrieren, führen Sie den folgenden Befehl im CLI-Terminal des Blitzz-Replikanten aus:

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing
   ```

   Die Benutzeroberfläche des Replikanten zeigt den Status der Replikation an. Nachdem die Schemamigration und der Momentaufnahmevorgang abgeschlossen sind, zeigt der Status 100 % an. Nach Abschluss der Migration können Sie die Daten in der Azure Cosmos-Zieldatenbank überprüfen.

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/oracle-data-migration-output.png" alt-text="Ausgabe der Oracle-Datenmigration":::

1. Da Sie den vollständigen Modus für die Migration verwendet haben, können Sie Vorgänge wie Einfügen, Aktualisieren oder Löschen von Daten in der Oracle-Quelldatenbank ausführen. Später können Sie überprüfen, ob sie in Echtzeit in die Azure Cosmos-Zieldatenbank repliziert werden. Stellen Sie nach der Migration sicher, dass Sie den für Ihren Azure Cosmos-Container konfigurierten Durchsatz verringern.

1. Sie können den Replikanten zu jedem Zeitpunkt beenden und mit dem Schalter **--resume** neu starten. Die Replikation wird an dem Punkt fortgesetzt, an dem Sie beendet wurde, ohne die Datenkonsistenz zu beeinträchtigen. Die folgenden Befehle zeigen, wie der Schalter „--resume“ zu verwenden ist.

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing --resume
   ```

Weitere Informationen zum Migrieren von Daten zu Zielen und zur Echtzeitmigration finden Sie in der [Blitzz-Replikant – Demo](https://www.youtube.com/watch?v=y5ZeRK5A-MI).

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen des Durchsatzes für Container und Datenbanken](set-throughput.md) 
* [Bewährte Methoden für Partitionsschlüssel](partitioning-overview.md#choose-partitionkey)
* Artikel zum [Schätzen des Durchsatzes (RU/s) mit dem Azure Cosmos DB Capacity Planner](estimate-ru-with-capacity-planner.md)
