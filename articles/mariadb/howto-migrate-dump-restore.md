---
title: Migrieren durch Sicherungen und Wiederherstellungen – Azure Database for MariaDB
description: In diesem Artikel werden zwei Möglichkeiten zum Sichern und Wiederherstellen von Datenbanken in Ihrer Instanz von Azure Database for MariaDB beschrieben, indem Tools wie mysqldump, MySQL Workbench und PHPMyAdmin genutzt werden.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 2/27/2020
ms.openlocfilehash: 6c23b027b428ef58ae51a6ba1d2603e94b1eaaf8
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540857"
---
# <a name="migrate-your-mariadb-database-to-azure-database-for-mariadb-using-dump-and-restore"></a>Migrieren der MariaDB-Datenbank auf Azure Database for MariaDB durch Sicherungen und Wiederherstellungen
In diesem Artikel werden zwei allgemeine Verfahren zum Sichern und Wiederherstellen von Datenbanken in Ihrer Instanz von Azure Database for MariaDB beschrieben.
- Sichern und Wiederherstellen über die Befehlszeile (mithilfe von „mysqldump“) 
- Sichern und Wiederherstellen mithilfe von PHPMyAdmin

## <a name="before-you-begin"></a>Voraussetzungen
Um diese Anleitung schrittweise auszuführen, müssen Sie Folgendes durchgeführt haben:
- [Erstellung eines Azure Database for MariaDB-Servers – Azure-Portal](quickstart-create-mariadb-server-database-using-azure-portal.md)
- Installation des Befehlszeilenprogramms [mysqldump](https://mariadb.com/kb/en/library/mysqldump/) auf einem Computer
- MySQL Workbench [Download von MySQL Workbench](https://dev.mysql.com/downloads/workbench/) oder einem anderen MySQL-Drittanbietertool zur Ausführung von Sicherungs- und Wiederherstellungsbefehlen.

## <a name="use-common-tools"></a>Verwenden von gängigen Tools
Mithilfe von gängigen Hilfsprogrammen und Tools wie MySQL Workbench oder mysqldump können Sie eine Remoteverbindung zu Azure Database for MariaDB herstellen und Daten darin wiederherstellen. Verwenden Sie diese Tools auf Ihrem Clientcomputer mit Internetverbindung, um eine Verbindung zu Azure Database for MariaDB herzustellen. Um eine SSL-verschlüsselte Verbindung gemäß bewährter Sicherheitsmethoden zu verwenden, lesen Sie [Konfigurieren von SSL-Konnektivität in Azure Database for MariaDB](concepts-ssl-connection-security.md). Bei der Migration zu Azure Database for MariaDB müssen Sie Ihre Sicherungsdateien nicht in einen bestimmten Cloudspeicherort verschieben. 

## <a name="common-uses-for-dump-and-restore"></a>Gängige Nutzungsszenarien für die Sicherung von Abbildern und die Wiederherstellung
Sie können MySQL-Hilfsprogramme wie mysqldump und mysqlpump verwenden, um Datenbanken in mehreren häufigen Szenarien zu sichern und in einen Azure Database for MariaDB-Server zu laden. 

<!--In other scenarios, you may use the [Import and Export](howto-migrate-import-export.md) approach instead.-->

- Verwenden Sie Sicherungen von Datenbankabbildern, wenn Sie die gesamte Datenbank migrieren. Diese Empfehlung gilt, wenn Sie eine große Menge von Daten verschieben oder die Dienstunterbrechung für Live-Websites oder -Anwendungen minimieren möchten. 
-  Stellen Sie sicher, dass für alle Tabellen in der Datenbank die InnoDB-Speicher-Engine verwendet wird, wenn Daten in Azure Database for MariaDB geladen werden. Azure Database for MariaDB unterstützt nur die InnoDB-Speicher-Engine und bietet keine Unterstützung für alternative Speicher-Engines. Wenn Ihre Tabellen mit anderen Speicher-Engines konfiguriert werden, sollten Sie diese in das InnoDB-Engine-Format konvertieren, bevor Sie die Migration zu Azure Database for MariaDB durchführen.
   Wenn Sie beispielsweise eine WordPress oder WebApp mit Verwendung der MyISAM-Tabellen nutzen, sollten Sie diese Tabellen zuerst konvertieren, indem Sie vor dem Wiederherstellen in Azure Database for MariaDB die Migration zum InnoDB-Format durchführen. Verwenden Sie die `ENGINE=InnoDB`-Klausel zum Festlegen der Engine, die beim Erstellen einer neuen Tabelle verwendet wird, und übertragen Sie die Daten vor der Wiederherstellung dann in die kompatible Tabelle. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Stellen Sie zur Verhinderung von Kompatibilitätsproblemen sicher, dass beim Sichern von Datenbankabbildern auf den Quell- und Zielsystemen die gleiche Version von MariaDB verwendet wird. Wenn Ihr vorhandener MariaDB-Server beispielsweise Version 10.2 hat, sollten Sie zu einer Azure Database for MariaDB-Instanz migrieren, die für die Ausführung von Version 10.2 konfiguriert ist. Der Befehl `mysql_upgrade` funktioniert auf einem Azure Database for MariaDB-Server nicht und wird nicht unterstützt. Wenn Sie ein übergreifendes Upgrade für MariaDB-Versionen durchführen müssen, sollten Sie die niedrigere Version Ihrer Datenbank in Ihrer eigenen Umgebung zuerst in einer höheren Version von MariaDB sichern bzw. dahin exportieren. Führen Sie anschließend `mysql_upgrade` aus, bevor Sie versuchen, die Migration zu einer Azure Database for MariaDB-Instanz durchzuführen.

## <a name="performance-considerations"></a>Überlegungen zur Leistung
Beachten Sie beim Sichern der Abbilder von großen Datenbanken die folgenden Aspekte, um die Leistung zu optimieren:
-   Verwenden Sie die Option `exclude-triggers` in mysqldump, wenn Sie Sicherungsabbilder von Datenbanken erstellen. Schließen Sie Trigger aus Abbilddateien aus, um zu verhindern, dass die Triggerbefehle während der Wiederherstellung der Daten ausgelöst werden. 
-   Verwenden Sie die Option `single-transaction`, um den Transaktionsisolationsmodus auf REPEATABLE READ festzulegen und um die SQL-Anweisung START TRANSACTION an den Server zu senden, bevor die Daten gesichert werden. Durch das Sichern vieler Tabellen in einer Transaktion wird bei der Wiederherstellung zusätzlicher Speicher benötigt. Die Optionen `single-transaction` und `lock-tables` sind wechselseitig exklusiv, da LOCK TABLES dazu führt, dass alle ausstehenden Transaktionen implizit committet werden. Um große Tabellen zu sichern, kombinieren Sie die Optionen `single-transaction` und `quick`. 
-   Verwenden Sie die Syntax für mehrere Zeilen `extended-insert`, die mehrere VALUE-Listen enthält. Dies führt zu einer kleineren Speicherabbilddatei und beschleunigt Einfügevorgänge beim erneuten Laden der Datei.
-  Verwenden Sie in mysqldump die Option `order-by-primary`, wenn Sie Datenbanken sichern, damit die Skripterstellung für die Daten in der Primärschlüssel-Reihenfolge erfolgt.
-   Verwenden Sie in mysqldump beim Erstellen von Sicherungsabbildern für Daten die Option `disable-keys`, um vor dem Laden Fremdschlüsseleinschränkungen zu deaktivieren. Das Deaktivieren von Fremdschlüsselüberprüfungen führt zu Leistungssteigerungen. Aktivieren Sie die Einschränkungen, und überprüfen Sie die Daten nach dem Laden, um die referentielle Integrität sicherzustellen.
-   Verwenden Sie ggf. partitionierte Tabellen.
-   Laden Sie Daten parallel. Vermeiden Sie zu viel Parallelität, da dies zum Erreichen eines Ressourcenlimits führen kann, und überwachen Sie die Ressourcen mit den Metriken, die im Azure-Portal verfügbar sind. 
-   Verwenden Sie in mysqlpump die Option `defer-table-indexes`, wenn Sie Sicherungsabbilder von Datenbanken erstellen, damit die Indexerstellung nach dem Laden der Tabellendaten erfolgt.
-   Kopieren Sie die Sicherungsdateien in einen Azure-Blob/-Speicher, und führen Sie die Wiederherstellung dort aus. Das sollte um einiges schneller sein als die Wiederherstellung über das Internet.

## <a name="create-a-backup-file"></a>Erstellen einer Sicherungsdatei
Führen Sie den Befehl „mysqldump“ aus, um eine vorhandene MariaDB-Datenbank auf dem lokalen Server oder einem virtuellen Computer zu sichern: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Es müssen folgende Parameter bereitgestellt werden:
- [uname] Der Datenbankbenutzername 
- [pass] Das Kennwort für Ihre Datenbank (beachten Sie, dass kein Leerzeichen zwischen „-p“ und dem Kennwort verwendet wird) 
- [dbname] Der Name der Datenbank 
- [backupfile.sql] Der Dateiname für die Datenbanksicherung 
- [--opt] Die mysqldump-Option 

Um eine Datenbank namens „testdb“ mit dem Benutzernamen „testuser“ auf Ihrem MariaDB-Server ohne Kennwort als Datei mit dem Namen „testdb_backup.sql“ zu sichern, verwenden Sie den folgenden Befehl. Mit dem Befehl wird die Datenbank `testdb` in einer Datei mit dem Namen `testdb_backup.sql` gesichert, die alle SQL-Anweisungen enthält, die zum erneuten Erstellen der Datenbank erforderlich sind. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Um zur Sicherung bestimmte Tabellen in der Datenbank auszuwählen, listen Sie die Tabellennamen durch Leerzeichen getrennt auf. Um beispielsweise nur die Tabellen „table1“ und „table2“ von „testdb“ zu sichern, führen Sie folgendes Beispiel durch: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Um mehrere Datenbanken gleichzeitig zu sichern, verwenden Sie den Schalter „--database“ und listen Sie die Datenbanknamen durch Leerzeichen getrennt auf. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>Erstellen einer Datenbank auf dem Zielserver
Erstellen Sie eine leere Datenbank auf dem Azure Database for MariaDB-Zielserver, zu dem Sie die Daten migrieren möchten. Verwenden Sie ein Tool wie MySQL Workbench zum Erstellen der Datenbank. Die Datenbank kann denselben Namen aufweisen wie die Datenbank, die die Sicherungsdaten enthält, oder Sie können eine Datenbank mit einem anderen Namen erstellen.

Ermitteln Sie zum Herstellen der Verbindung die Verbindungsinformationen in der **Übersicht** Ihrer Azure Database for MariaDB-Instanz.

![Ermitteln der Verbindungsinformationen im Azure-Portal](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

Fügen Sie die Verbindungsinformationen in MySQL-Workbench hinzu.

![MySQL Workbench-Verbindungszeichenfolge](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>Wiederherstellen der MariaDB-Datenbank
Nachdem Sie die Zieldatenbank erstellt haben, können Sie mithilfe des Befehls „mysql“ oder von MySQL Workbench Daten in der jeweiligen neu erstellten Datenbank anhand der Sicherungsdatei wiederherstellen.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
In diesem Beispiel stellen Sie die Daten in der neu erstellten Datenbank auf dem Azure Database for MariaDB-Server wieder her.
```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Exportieren mithilfe von PHPMyAdmin
Für den Export können Sie das allgemeine Tool phpMyAdmin verwenden, das Sie eventuell bereits lokal in Ihrer Umgebung installiert haben. So exportieren Sie Ihre MariaDB-Datenbank mithilfe von PHPMyAdmin:
1. Öffnen Sie phpMyAdmin.
2. Wählen Sie Ihre Datenbank aus. Klicken Sie in der Liste links auf den Datenbanknamen. 
3. Klicken Sie auf den Link **Export**. Eine neue Seite mit dem Speicherabbild der Datenbank wird angezeigt.
4. Klicken Sie im Bereich „Export“ auf den Link **Alle auswählen**, um die Tabellen in Ihrer Datenbank auszuwählen. 
5. Klicken Sie im Bereich mit den SQL-Optionen auf die gewünschten Optionen. 
6. Klicken Sie auf die Option **Als Datei speichern** und die entsprechende Komprimierungsoption. Klicken Sie dann auf die Schaltfläche **Gehe zu**. Es sollte ein Dialogfeld angezeigt werden, in dem Sie dazu aufgefordert werden, die Datei lokal zu speichern.

## <a name="import-using-phpmyadmin"></a>Importieren mithilfe von PHPMyAdmin
Der Import der Datenbank ist mit dem Exportvorgang vergleichbar. Führen Sie folgende Aktionen durch:
1. Öffnen Sie phpMyAdmin. 
2. Klicken Sie auf der phpMyAdmin-Setupseite auf **Hinzufügen**, um Ihre Azure Database for MariaDB-Serverinstanz hinzuzufügen. Geben Sie die Verbindungsdetails und Anmeldeinformationen an.
3. Erstellen Sie eine passend benannte Datenbank, und wählen Sie sie links auf dem Bildschirm aus. Klicken Sie zum erneuten Generieren der vorhandenen Datenbank auf den Datenbanknamen, aktivieren Sie alle Kontrollkästchen neben den Tabellennamen, und wählen Sie **Drop** (Verwerfen), um die vorhandenen Tabellen zu löschen. 
4. Klicken Sie auf den Link **SQL**, um die Seite anzuzeigen, auf der Sie SQL-Befehle eingeben oder Ihre SQL-Datei hochladen können. 
5. Verwenden Sie die Schaltfläche **Durchsuchen**, um nach der Datenbankdatei zu suchen. 
6. Klicken Sie auf die Schaltfläche **Go** (Los), um die Sicherung zu exportieren, die SQL-Befehle auszuführen und die Datenbank neu zu erstellen.

## <a name="next-steps"></a>Nächste Schritte
- [Herstellen einer Verbindung zwischen Anwendungen und Azure Database for MariaDB](./howto-connection-string.md).
 
<!--
- For more information about migrating databases to Azure Database for MariaDB, see the [Database Migration Guide](https://aka.ms/datamigration).
-->
