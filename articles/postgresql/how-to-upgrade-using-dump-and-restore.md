---
title: Upgrade mithilfe von Sicherung und Wiederherstellung – Azure Database for PostgreSQL – Single Server
description: Beschreibt, wie Offlineupgrades für Datenbanken mithilfe der Sicherungs- und Wiederherstellungsfunktionen durchgeführt werden, um zu einer höheren Version von Azure Database for PostgreSQL – Single Server zu migrieren.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: 42bbe1c9f4056ae0dae0ccd59b452db90a7c63c5
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493660"
---
# <a name="upgrade-your-postgresql-database-using-dump-and-restore"></a>Upgrade einer PostgreSQL-Datenbank durch Sichern und Wiederherstellen

Sie können einen in Azure Database for PostgreSQL – Single Server bereitgestellten PostgreSQL-Server aktualisieren, indem Sie Ihre Datenbanken mithilfe der folgenden Methoden zu einem Server mit einer höheren Hauptversion migrieren.
* **Offlinemethode** mit [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) und [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) von PostgreSQL – die Datenmigration verursacht eine Downtime. Im vorliegenden Dokument wird diese Upgrade-/Migrationsmethode behandelt.
* **Onlinemethode** mit [Database Migration Service](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) (DMS). Bei dieser Methode erfolgt die Migration mit einer geringeren Downtime. Außerdem bleibt die Zieldatenbank mit der Quelldatenbank synchron, und Sie können wählen, wann der Übergang erfolgen soll. Allerdings müssen bei Verwendung von DMS einige Voraussetzungen und Einschränkungen beachtet werden. Ausführliche Informationen finden Sie in der [DMS-Dokumentation](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md). 

 In der folgenden Tabelle finden Sie Empfehlungen, die auf der Größe und dem Verwendungsszenario der Datenbank basieren.

| **Datenbank/Szenario** | **Sichern/Wiederherstellen (offline)** | **DMS (online)** |
| ------ | :------: | :-----: |
| Sie haben eine kleine Datenbank und können von einem Upgrade verursachte Ausfallzeiten verkraften  | X | |
| Kleine Datenbanken (< 10 GB)  | X | X | 
| Kleine bis mittelgroße Datenbanken (10 GB bis 100 GB) | X | X |
| Große Datenbanken (> 100 GB) |  | X |
| Können von einem Upgrade verursachte Ausfallzeiten (unabhängig von der Datenbankgröße) verkraftet werden? | X |  |
| Können [Voraussetzungen](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md#prerequisites) für DMS einschließlich Neustart erfüllt werden? |  | X |
| Können DDLs und nicht protokollierte Tabellen während des Upgradevorgangs vermieden werden? | |  X |

Dieser Leitfaden enthält einige Methoden und Beispiele für die Offlinemigration, um zu veranschaulichen, wie Sie vom Quellserver zum Zielserver migrieren können, auf dem eine höhere Version von PostgreSQL ausgeführt wird.

> [!NOTE]
> Die PostgreSQL-Sicherung und -Wiederherstellung kann auf verschiedene Weisen erfolgen. Sie können sich für eine Migration mit einer der in diesem Leitfaden angebotenen Methoden entscheiden oder alternative Möglichkeiten wählen, die Ihren Bedürfnissen entsprechen. Die detaillierte Syntax für Sicherung und Wiederherstellung mit zusätzlichen Parametern finden Sie in den Artikeln [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) und [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html). 


## <a name="prerequisites-for-using-dump-and-restore-with-azure-database-for-postgresql"></a>Voraussetzungen für die Sicherung und Wiederherstellung mit Azure Database for PostgreSQL
 
Zum Ausführen der Schritte in dieser Anleitung benötigen Sie Folgendes:

- Eine PostgreSQL-**Quelldatenbank** mit Version 9.5, 9.6 oder 10, für die Sie ein Upgrade ausführen möchten
- Ein PostgreSQL-[Zieldatenbankserver](quickstart-create-server-database-portal.md) mit der gewünschten Hauptversion von **Azure Database for PostgreSQL** 
- Ein PostgreSQL-Clientsystem zum Ausführen der Sicherungs- und Wiederherstellungsbefehle
  - Dabei kann es sich um einen Linux- oder Windows-Client handeln, auf dem PostgreSQL sowie die Befehlszeilenprogramme [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) und [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) installiert sind. 
  - Alternativ können Sie [Azure Cloud Shell](https://shell.azure.com) verwenden, indem Sie auf der Menüleiste rechts oben im [Azure-Portal](https://portal.azure.com) auf Azure Cloud Shell klicken. Sie müssen sich bei Ihrem Konto `az login` anmelden, ehe Sie die Befehle zur Sicherung und Wiederherstellung ausführen können.
- Ihr PostgreSQL-Client, der vorzugsweise in derselben Region wie der Quell- und Zielserver ausgeführt wird 


## <a name="additional-details-and-considerations"></a>Weitere Details und Überlegungen
- Sie können die Verbindungszeichenfolge für die Quell- und Zieldatenbank finden, indem Sie im Portal auf „Verbindungszeichenfolgen“ klicken. 
- Möglicherweise führen Sie mehr als eine Datenbank auf dem Server aus. Sie finden die Liste der Datenbanken, indem Sie eine Verbindung mit dem Quellserver herstellen und `\l` ausführen.
- Erstellen Sie entsprechende Datenbanken auf dem Zieldatenbankserver.
- Sie können das Upgrade von `azure_maintenance` -oder Vorlagendatenbanken überspringen.
- Anhand der obigen Tabellen können Sie feststellen, ob die Datenbank für diesen Migrationsmodus geeignet ist.
- Wenn Sie Azure Cloud Shell verwenden möchten, sollten Sie beachten, dass die Sitzung nach 20 Minuten ein Timeout verursacht. Wenn die Datenbankgröße kleiner als 10 GB ist, können Sie das Upgrade möglicherweise ohne ein Timeout der Sitzung abschließen. Andernfalls müssen Sie die Sitzung möglicherweise durch andere Mittel geöffnet halten, z. B. durch einmaliges Drücken der <Enter>-Taste alle 10-15 Minuten. 


## <a name="example-database-used-in-this-guide"></a>In dieser Anleitung verwendete Beispieldatenbank

In den Beispielen in diesem Leitfaden werden zur Veranschaulichung die folgenden Quellserver-, Zielserver- und Datenbanknamen verwendet.

 | **Beschreibung** | **Wert** |
 | ------- | ------- |
 | Quellserver (Version 9.5) | pg-95.postgres.database.azure.com |
 | Quelldatenbank | bench5gb |
 | Größe der Quelldatenbank | 5 GB |
 | Quellbenutzername | pg@pg-95 |
 | Zielserver (Version 11) | pg-11.postgres.database.azure.com |
 | Zieldatenbank | bench5gb |
 | Zielbenutzername | pg@pg-11 |

## <a name="upgrade-your-databases-using-offline-migration-methods"></a>Datenbankupgrade mithilfe von Offlinemigrationsmethoden
Sie können eine der in diesem Abschnitt beschriebenen Methoden für Ihre Upgrades verwenden. Sie können die folgenden Tipps bei der Durchführung der Aufgaben nutzen.

- Wenn Sie für Quell- und Zieldatenbank dasselbe Kennwort verwenden, können Sie die Umgebungsvariable `PGPASSWORD=yourPassword` festlegen.  Dann müssen Sie nicht jedes Mal ein Kennwort angeben, wenn Sie Befehle wie „psql“, „pg_dump“ und „pg_restore“ ausführen.  Ebenso können Sie zusätzliche Variablen wie `PGUSER`, `PGSSLMODE` usw. einrichten. Weitere Informationen finden Sie unter [PostgreSQL-Umgebungsvariablen](https://www.postgresql.org/docs/11/libpq-envars.html).
  
- Wenn Ihr PostgreSQL-Server TLS-/SSL-Verbindungen erfordert (auf Azure Database for PostgreSQL-Servern standardmäßig aktiviert), legen Sie eine Umgebungsvariable `PGSSLMODE=require` fest, damit das pg_restore-Tool über TLS eine Verbindung herstellt. Erfolgt die Verbindung nicht über TLS, kann der Fehler möglicherweise wie folgt lauten: `FATAL:  SSL connection is required. Please specify SSL options and retry.`

- Führen Sie in der Windows-Befehlszeile den Befehl `SET PGSSLMODE=require` aus, bevor Sie den Befehl „pg_restore“ ausführen. Führen Sie unter Linux oder Bash den Befehl `export PGSSLMODE=require` aus, bevor Sie den Befehl „pg_restore“ ausführen.

### <a name="method-1-migrate-using-dump-file"></a>Methode 1: Migrieren mit einer Sicherungsdatei

Diese Methode umfasst zwei Schritte. Zuerst wird eine Sicherung vom Quellserver erstellt. Der zweite Schritt besteht darin, die Sicherungsdatei auf dem Zielserver wiederherzustellen. Weitere Informationen finden Sie in der Dokumentation [Migrieren durch Sicherungen und Wiederherstellungen](howto-migrate-using-dump-and-restore.md). Dies ist die empfohlene Methode, wenn Sie über umfangreiche Datenbanken verfügen und Ihr Clientsystem genügend Speicherplatz für die Speicherung der Sicherungsdatei aufweist.

### <a name="method-2-migrate-using-streaming-the-dump-data-to-the-target-database"></a>Methode 2: Migrieren durch das Streamen der Sicherungsdaten in die Zieldatenbank

Wenn Sie keinen PostgreSQL-Client besitzen oder Azure Cloud Shell verwenden möchten, können Sie diese Methode nutzen. Die Datenbanksicherung wird direkt auf den Zieldatenbankserver gestreamt, ohne dass die Sicherung auf dem Client gespeichert wird. Folglich kann diese Methode bei einem Client mit begrenztem Speicherplatz verwendet und sogar über Azure Cloud Shell ausgeführt werden. 

1. Vergewissern Sie sich mit dem Befehl `\l`, dass die Datenbank auf dem Zielserver vorhanden ist. Wenn die Datenbank nicht vorhanden ist, erstellen Sie sie.
   ```azurecli-interactive
    psql "host=myTargetServer port=5432 dbname=postgres user=myUser password=###### sslmode=mySSLmode"
    ```
    ```SQL
    postgres> \l   
    postgres> create database myTargetDB;
   ```

2. Führen Sie die Sicherung und Wiederherstellung als einzelne Befehlszeile mithilfe einer Pipe aus. 
    ```azurecli-interactive
    pg_dump -Fc -v --mySourceServer --port=5432 --username=myUser --dbname=mySourceDB | pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB
    ```

    Beispiel:

    ```azurecli-interactive
    pg_dump -Fc -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb | pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb
    ```  
3. Nach Abschluss des Prozesses zum Upgrade (Migration) können Sie Ihre Anwendung auf dem Zielserver testen. 
4. Wiederholen Sie diesen Vorgang für alle Datenbanken auf dem Server.

 Die folgende Tabelle veranschaulicht beispielsweise, wie lange die Migration mit der Sicherungsmethode per Streaming gedauert hat. Die Beispieldaten werden mithilfe von [pgbench](https://www.postgresql.org/docs/10/pgbench.html) aufgefüllt. Da Ihre Datenbank eine andere Anzahl von Objekten mit unterschiedlichen Größen als die von pgbench generierten Tabellen und Indizes enthalten kann, wird dringend empfohlen, die Befehle zur Sicherung und Wiederherstellung Ihrer Datenbank zu testen, um die tatsächliche Dauer der Aktualisierung Ihrer Datenbank zu ermitteln. 

| **Datenbankgröße** | **Ca. benötigte Zeit** | 
| ----- | ------ |
| 1 GB  | 1-2 Minuten |
| 5 GB | 8-10 Minuten |
| 10 GB | 15-20 Minuten |
| 50 GB | 1-1,5 Stunden |
| 100 GB | 2,5-3 Stunden|
   
### <a name="method-3-migrate-using-parallel-dump-and-restore"></a>Method 3: Migrieren durch parallele Sicherungen und Wiederherstellungen 

Diese Methode eignet sich, wenn die Datenbank nur wenige größere Tabellen enthält und Sie den Sicherungs- und Wiederherstellungsprozess für diese Datenbank parallel ausführen möchten. Außerdem benötigen Sie auf Ihrem Clientsystem ausreichend Speicherplatz für die Sicherungen. Dieser parallele Sicherungs- und Wiederherstellungsprozess verringert den Zeitaufwand für die gesamte Migration. Beispielsweise wurde der Vorgang für die 50 GB große pgbench-Datenbank, deren Migration mit Methode 1 und 2 zwischen 1 und 1,5 Stunden dauerte, mit dieser Methode in weniger als 30 Minuten abgeschlossen.

1. Erstellen Sie für jede Datenbank auf dem Quellserver eine entsprechende Datenbank auf dem Zielserver.

   ```bash
    psql "host=myTargetServer port=5432 dbname=postgres user=myuser password=###### sslmode=mySSLmode"
    postgresl> create database myDB;
   ```
   Beispiel:
    ```bash
    psql "host=pg-11.postgres.database.azure.com port=5432 dbname=postgres user=pg@pg-11 password=###### sslmode=require"

    postgres> create database bench5gb;
    postgres> \q
    ```

2. Führen Sie den Befehl pg_dump in einem Verzeichnisformat mit „Anzahl der Aufträge = 4 (Anzahl der Tabellen in der Datenbank)“ aus. Bei einem höheren Computetarif und mehr Tabellen können Sie diesen Wert steigern. Mit diesem Befehl pg_dump wird ein Verzeichnis erstellt, in dem komprimierte Dateien für jeden Auftrag gespeichert werden.

    ```bash
    pg_dump -Fd -v --host=sourceServer --port=5432 --username=myUser --dbname=mySourceDB -j 4 -f myDumpDirectory
    ```
    Beispiel:
    ```bash
    pg_dump -Fd -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb -j 4 -f dump.dir
    ```

3. Stellen Sie anschließend die Sicherung auf dem Zielserver wieder her.
    ```bash
    $ pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB -j 4 myDumpDir
    ```
    Beispiel:
    ```bash
    $ pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb -j 4 dump.dir
    ```

> [!TIP]
> Der in diesem Dokument erwähnte Prozess kann auch zum Einsatz kommen, um Ihre Instanz von Azure Database for PostgreSQL – Flexibler Server (in der Vorschau) zu aktualisieren. Der Hauptunterschied besteht darin, dass die Verbindungszeichenfolge für das Ziel auf dem flexiblen Server `@dbName` nicht enthält.  Wenn der Benutzername beispielsweise `pg` ist, lautet der Benutzername des einzelnen Servers in der Verbindungszeichenfolge `pg@pg-95`, während Sie bei einem flexiblen Server einfach `pg` verwenden können.

## <a name="next-steps"></a>Nächste Schritte

- Wenn Sie mit der Funktionsweise der Zieldatenbank zufrieden sind, können Sie Ihren alten Datenbankserver löschen. 
- Wenn Sie den gleichen Datenbankendpunkt wie der Quellserver verwenden möchten, können Sie, nachdem Sie Ihren alten Quelldatenbankserver gelöscht haben, ein Lesereplikat mit dem Namen des alten Datenbankservers erstellen. Sobald der Zustand stabil ist, können Sie das Replikat beenden, wodurch der Replikatserver zu einem unabhängigen Server höher gestuft wird. Weitere Informationen finden Sie unter [Replikation](./concepts-read-replicas.md).
- Denken Sie daran, diese Befehle in einer Testumgebung zu testen und zu überprüfen, bevor Sie sie in der Produktionsumgebung verwenden.