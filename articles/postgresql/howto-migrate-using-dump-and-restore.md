---
title: Sichern und Wiederherstellen in Azure Database for PostgreSQL (Einzelserver)
description: In diesem Artikel wird beschrieben, wie Sie eine PostgreSQL-Datenbank in eine Sicherungsdatei extrahieren und mithilfe einer mit pg_dump erstellten Datei in Azure Database for PostgreSQL (Einzelserver) wiederherstellen.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: aa9485ec8fcabdc0276e0598bd3e19f04d70dfa1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65066987"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Migrieren der PostgreSQL-Datenbank durch Sichern und Wiederherstellen
Sie können mit [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) eine PostgreSQL-Datenbank in eine Sicherungsdatei extrahieren und mit [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) die PostgreSQL-Datenbank aus einer mit pg_dump erstellten Archivdatei wiederherstellen.

## <a name="prerequisites"></a>Voraussetzungen
Zum Ausführen der Schritte in dieser Anleitung benötigen Sie Folgendes:
- Einen [Server für Azure-Datenbank für PostgreSQL](quickstart-create-server-database-portal.md) mit Firewallregeln, um den Zugriff und eine Datenbank darunter zu ermöglichen
- Die Befehlszeilenprogramme [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) und [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) müssen installiert sein.

Führen Sie zum Sichern und Wiederherstellen der PostgreSQL-Datenbank diese Schritte aus:

## <a name="create-a-dump-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>Erstellen einer Sicherungsdatei mit pg_dump, die die zu ladenden Daten enthält
Um eine vorhandene PostgreSQL-Datenbank lokal oder auf einem virtuellen Computer zu sichern, führen Sie folgenden Befehl aus:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> > <database>.dump
```
Beispiel: Lokaler Server und eine Datenbank namens **testdb**
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb > testdb.dump
```


## <a name="restore-the-data-into-the-target-azure-database-for-postrgesql-using-pgrestore"></a>Wiederherstellen der Daten in der Azure-Zieldatenbank für PostgreSQL mithilfe von pg_restore
Nachdem Sie die Zieldatenbank erstellt haben, können Sie den Befehl „pg_restore“ und den „-d“-Parameter „--dbname“ verwenden, um die Daten in der Zieldatenbank anhand der Sicherungsdatei wiederherzustellen.
```bash
pg_restore -v --no-owner –-host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
Das Einschließen des Parameters „--no-owner“ bewirkt, dass sich alle Objekte, die während der Wiederherstellung erstellt wurden, im Besitz des mit „--username“ angegebenen Benutzers befinden. Weitere Informationen finden Sie in der offiziellen PostgreSQL-Dokumentation auf [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> Wenn Ihr PostgreSQL-Server SSL-Verbindungen erfordert (auf Azure Database for PostgreSQL-Servern standardmäßig aktiviert), legen Sie eine Umgebungsvariable `PGSSLMODE=require` fest, damit das pg_restore-Tool über SSL eine Verbindung herstellt. Erfolgt die Verbindung nicht über SSL, kann der Fehler möglicherweise wie folgt lauten: `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> Führen Sie in der Windows-Befehlszeile den Befehl `SET PGSSLMODE=require` aus, bevor Sie den Befehl „pg_restore“ ausführen. Führen Sie unter Linux oder Bash den Befehl `export PGSSLMODE=require` aus, bevor Sie den Befehl „pg_restore“ ausführen.
>

In diesem Beispiel stellen Sie die Daten aus der Sicherungsdatei **testdb.dump** in der Datenbank **mypgsqldb** auf dem Zielserver **mydemoserver.postgres.database.azure.com** wieder her. 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="optimizing-the-migration-process"></a>Optimieren des Migrationsprozesses

Eine Möglichkeit zum Migrieren Ihrer vorhandenen PostgreSQL-Datenbank zu Azure Database for PostgreSQL besteht darin, die Datenbank auf dem Quellcomputer zu sichern und in Azure wiederherzustellen. Um die erforderliche Zeit zum Abschließen der Migration zu minimieren, sollten Sie die folgenden Parameter mit den Befehlen zum Sichern und Wiederherstellen verwenden.

> [!NOTE]
> Ausführliche Informationen über die Syntax finden Sie in den Artikeln zu [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) und [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>Für die Sicherung
- Erstellen Sie die Sicherung mit dem Switch „-Fc“, damit Sie die Wiederherstellung parallel durchführen können, um den Vorgang zu beschleunigen. Beispiel:

    ```
    pg_dump -h MySourceServerName -U MySourceUserName -Fc -d MySourceDatabaseName > Z:\Data\Backups\MyDatabaseBackup.dump
    ```

### <a name="for-the-restore"></a>Für die Wiederherstellung
- Es wird empfohlen, dass Sie die Sicherungsdatei in eine Azure-VM in der gleichen Region des Azure Database for PostgreSQL-Servers verschieben, zu dem Sie migrieren, und den Befehl „pg_restore“ über diese VM ausführen, um die Netzwerklatenz zu reduzieren. Außerdem wird empfohlen, dass Sie die VM mit aktiviertem [beschleunigtem Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-powershell.md) erstellen.

- Dies sollte zwar der Standardeinstellung entsprechen, öffnen Sie jedoch trotzdem die Sicherungsdatei, um zu überprüfen, dass die Create Index-Anweisungen sich hinter dem Einfügen der Daten befinden. Wenn das nicht der Fall ist, verschieben Sie die Create Index-Anweisungen hinter das Einfügen der Daten.

- Führen Sie die Wiederherstellung mit den Switches „-Fc“ und „-j“ *#* durch, um die Wiederherstellung zu parallelisieren. *#* beschreibt die Anzahl von Kernen auf dem Zielserver. Sie können auch versuchen, mit *#* die doppelte Anzahl von Kernen des Zielservers anzugeben, um die Auswirkungen zu sehen. Beispiel:

    ```
    pg_restore -h MyTargetServer.postgres.database.azure.com -U MyAzurePostgreSQLUserName -Fc -j 4 -d MyTargetDatabase Z:\Data\Backups\MyDatabaseBackup.dump
    ```

- Sie können auch die Sicherungsdatei bearbeiten, indem Sie den Befehl *set synchronous_commit = off;* am Anfang und den Befehl *set synchronous_commit = on;* am Ende einfügen. Wenn Sie die Aktivierung nicht am Ende einfügen, bevor die Apps die Daten ändern, kann dies zu Datenverlust führen.

- Erwägen Sie auf dem Azure Database for PostgreSQL-Server vor der Wiederherstellung Folgendes auszuführen:
    - Deaktivieren Sie die Nachverfolgung der Abfrageleistung, da diese Statistiken während der Migration nicht benötigt werden. Dazu können Sie „pg_stat_statements.track“, „pg_qs.query_capture_mode“ und „pgms_wait_sampling.query_capture_mode“ auf „NONE“ festlegen.

    - Verwenden Sie eine SKU mit hoher Rechenleistung und viel Arbeitsspeicher, wie 32 arbeitsspeicheroptimierte virtuelle Kerne, um die Migration zu beschleunigen. Nachdem die Wiederherstellung abgeschlossen ist, können Sie die SKU problemlos wieder auf die bevorzugte Variante herunterstufen. Je höher die SKU, desto mehr Parallelität können Sie erreichen, indem Sie den entsprechenden Parameter `-j` im Befehl „pg_restore“ erhöhen. 

    - Mehr IOPS auf dem Zielserver könnten die Leistung bei der Wiederherstellung verbessern. Sie können mehr IOPS bereitstellen, indem Sie die Speichergröße des Servers erhöhen. Diese Einstellung ist nicht umkehrbar, aber bedenken Sie, ob Ihre tatsächliche Workload in Zukunft von einem höheren IOPS-Wert profitieren würde.

Denken Sie daran, diese Befehle in einer Testumgebung zu testen und zu überprüfen, bevor Sie sie in der Produktionsumgebung verwenden.

## <a name="next-steps"></a>Nächste Schritte
- Informationen zum Migrieren einer PostgreSQL-Datenbank mithilfe von Export und Import finden Sie unter [Migrieren der PostgreSQL-Datenbank mit Import und Export](howto-migrate-using-export-and-import.md).
- Weitere Informationen zum Migrieren von Datenbanken zu Azure Database for PostgreSQL finden Sie im [Leitfaden zur Datenbankmigration](https://aka.ms/datamigration).
