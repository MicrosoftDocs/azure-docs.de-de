---
title: Migrieren durch Sicherungen und Wiederherstellungen – Azure Database for MySQL
description: In diesem Artikel werden zwei Möglichkeiten zum Sichern und Wiederherstellen von Datenbanken in Ihrer Azure-Datenbank für MySQL beschrieben, indem Tools wie mysqldump, MySQL Workbench und PHPMyAdmin genutzt werden.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/30/2020
ms.openlocfilehash: f21587fe6a48d042ed98c126beb2a7dcaa39b7d8
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94537916"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Migrieren der MySQL-Datenbank auf Azure-Datenbank für MySQL durch Sicherungen und Wiederherstellungen

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

In diesem Artikel werden zwei allgemeine Verfahren zum Sichern und Wiederherstellen von Datenbanken in Azure-Datenbank für MySQL beschrieben
- Sichern und Wiederherstellen über die Befehlszeile (mithilfe von „mysqldump“)
- Sichern und Wiederherstellen mithilfe von PHPMyAdmin

Ausführliche Informationen und Anwendungsfälle für die Migration von Datenbanken zu Azure Database for MySQL finden Sie auch im [Leitfaden zur Datenbankmigration](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide). Dieser Leitfaden enthält wichtige Informationen zur erfolgreichen Planung und Ausführung einer MySQL-Migration zu Azure.

## <a name="before-you-begin"></a>Voraussetzungen
Um diese Anleitung schrittweise auszuführen, müssen Sie Folgendes durchgeführt haben:
- [Erstellung eines Servers für Azure-Datenbank für MySQL – Azure-Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- Installation des Befehlszeilenprogramms [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) auf einem Computer
- [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) oder einem anderen MySQL-Drittanbietertool zur Ausführung von Sicherungs- und Wiederherstellungsbefehlen.

> [!TIP]
> Wenn Sie große Datenbanken mit mehr als 1 TB migrieren möchten, können Sie die Verwendung von Communitytools wie **mydumper/myloader** in Erwägung ziehen, die den parallelen Export und Import unterstützen. Erfahren Sie mehr über das [Migrieren großer MySQL-Datenbanken](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699).


## <a name="common-use-cases-for-dump-and-restore"></a>Gängige Anwendungsfälle für die Sicherung und Wiederherstellung

Gängige Anwendungsfälle:

- **Wechsel von einem anderen Anbieter eines verwalteten Diensts:** Die meisten Anbieter verwalteter Dienste bieten aus Sicherheitsgründen möglicherweise keinen Zugriff auf die physische Speicherdatei, sodass für die Migration die logische Sicherung und Wiederherstellung die einzige Option darstellt.
- **Migration von einer lokalen Umgebung oder einem virtuellen Computer:** Azure Database for MySQL unterstützt nicht die Wiederherstellung physischer Sicherungen, sodass als EINZIGER Ansatz die logische Sicherung und Wiederherstellung infrage kommt.
- **Verschieben des Sicherungsspeichers von lokal redundantem in georedundanten Speicher:** Azure Database for MySQL ermöglicht das Konfigurieren von lokal redundantem oder georedundantem Speicher für die Sicherung – allerdings nur während der Servererstellung. Nachdem der Server bereitgestellt wurde, können Sie die Option für die Sicherungsspeicherredundanz nicht mehr ändern. Zum Verschieben Ihres Sicherungsspeichers aus lokal redundantem Speicher in georedundanten Speicher ist das Sichern und Wiederherstellen die EINZIGE Option. 
-  **Migration von alternativen Speicher-Engines zu InnoDB:** Azure Database for MySQL unterstützt nur die InnoDB-Speicher-Engine und somit keine alternativen Speicher-Engines. Wenn Ihre Tabellen mit anderen Speicher-Engines konfiguriert werden, sollten Sie diese in das InnoDB-Engine-Format konvertieren, bevor Sie die Migration zu Azure Database for MySQL durchführen.

    Wenn Sie beispielsweise eine WordPress oder WebApp mit Verwendung der MyISAM-Tabellen nutzen, sollten Sie diese Tabellen zuerst konvertieren, indem Sie vor dem Wiederherstellen der Azure-Datenbank für MySQL die Migration zum InnoDB-Format durchführen. Verwenden Sie die `ENGINE=InnoDB`-Klausel zum Festlegen der Engine, die beim Erstellen einer neuen Tabelle verwendet wird, und übertragen Sie die Daten vor der Wiederherstellung dann in die kompatible Tabelle.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
> [!Important]
> - Stellen Sie zur Verhinderung von Kompatibilitätsproblemen sicher, dass beim Sichern von Datenbankabbildern auf den Quell- und Zielsystemen die gleiche Version von MySQL verwendet wird. Wenn Ihr vorhandener MySQL-Server beispielsweise Version 5.7 hat, sollten Sie zu einer Azure-Datenbank für MySQL migrieren, die für die Ausführung von Version 5.7 konfiguriert ist. Der Befehl `mysql_upgrade` funktioniert auf einem Azure-Datenbank für MySQL-Server nicht und wird nicht unterstützt. 
> - Wenn Sie ein übergreifendes Upgrade für MySQL-Versionen durchführen müssen, sollten Sie die niedrigere Version Ihrer Datenbank in Ihrer eigenen Umgebung zuerst in einer höheren Version von MySQL sichern bzw. exportieren. Führen Sie anschließend `mysql_upgrade` aus, bevor Sie versuchen, die Migration zu einer Azure-Datenbank für MySQL durchzuführen.

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
-   Verwenden Sie in mysqlpump die Option `skip-definer`, um DEFINER- und SQL SECURITY-Klauseln in den CREATE-Anweisungen für Sichten und gespeicherte Prozeduren zu überspringen.  Wenn Sie die Speicherabbilddatei erneut laden, werden Objekte erstellt, für die die DEFINER- und SQL SECURITY-Standardwerte verwendet werden.
-   Kopieren Sie die Sicherungsdateien in einen Azure-Blob/-Speicher, und führen Sie die Wiederherstellung dort aus. Das sollte um einiges schneller sein als die Wiederherstellung über das Internet.

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Erstellen einer Datenbank auf dem Azure-Datenbank für MySQL-Zielserver
Erstellen Sie eine leere Datenbank auf dem Azure-Datenbank für MySQL-Zielserver, zu dem Sie die Daten migrieren möchten. Verwenden Sie ein Tool wie MySQL Workbench oder mysql.exe zum Erstellen der Datenbank. Die Datenbank kann denselben Namen aufweisen wie die Datenbank, die die Sicherungsdaten enthält, oder Sie können eine Datenbank mit einem anderen Namen erstellen.

Ermitteln Sie zum Herstellen der Verbindung die Verbindungsinformationen in der **Übersicht** Ihrer Azure Database for MySQL-Instanz.

:::image type="content" source="./media/concepts-migrate-dump-restore/1_server-overview-name-login.png" alt-text="Ermitteln der Verbindungsinformationen im Azure-Portal":::

Fügen Sie die Verbindungsinformationen in MySQL-Workbench hinzu.

:::image type="content" source="./media/concepts-migrate-dump-restore/2_setup-new-connection.png" alt-text="MySQL Workbench-Verbindungszeichenfolge":::

## <a name="preparing-the-target-azure-database-for-mysql-server-for-fast-data-loads"></a>Vorbereiten des Zielservers von Azure Database for MySQL für das schnelle Laden von Daten
Wenn Sie den Azure Database for MySQL-Zielserver für das schnellere Laden von Daten vorbereiten möchten, müssen Sie die folgenden Serverparameter und die Konfiguration ändern.
- Legen Sie max_allowed_packet auf „1073741824“ (also 1 GB) fest, um Überlaufprobleme aufgrund langer Zeilen zu vermeiden.
- Legen Sie slow_query_log auf „OFF“ fest, um die langsame Abfrageprotokollierung zu deaktivieren. Dadurch entfällt der zusätzliche Aufwand, der durch eine langsame Abfrageprotokollierung während des Ladens von Daten verursacht wird.
- Legen Sie „query_store_capture_mode“ auf „NONE“ fest, um den Abfragespeicher zu deaktivieren. Dadurch entfällt der Aufwand durch das Sampling von Aktivitäten durch den Abfragespeicher.
- Skalieren Sie den Server im Portal auf der Tarifseite während der Migration auf die arbeitsspeicheroptimierte SKU mit 32 virtuellen Kernen hoch, um die innodb_buffer_pool_size zu erhöhen. Die innodb_buffer_pool_size kann nur durch Hochskalieren von Computeressourcen für Azure Database for MySQL-Server erhöht werden.
- Ändern Sie „innodb_io_capacity“ und „innodb_io_capacity_max“ in den Serverparametern im Azure-Portal auf „9.000“, um die E/A-Auslastung zu verbessern und die Migrationsgeschwindigkeit damit zu optimieren.
- Ändern Sie „innodb_write_io_threads“ und „innodb_write_io_threads“ in den Serverparametern im Azure-Portal auf „4“, um die Migrationsgeschwindigkeit zu erhöhen.
- Skalieren Sie die Speicherebene hoch. Die IOPS für den Azure Database for MySQL-Server erhöhen sich progressiv mit dem Anstieg der Speicherebene. Für schnellere Ladevorgänge empfiehlt es sich, die Speicherebene zu erhöhen, um die bereitgestellten IOPS zu steigern. Beachten Sie jedoch, dass der Speicher nur hochskaliert, aber nicht herunterskaliert werden kann.

Nach Abschluss der Migration können Sie die Serverparameter und die Konfiguration der Computeebene auf die vorherigen Werte zurücksetzen.

## <a name="dump-and-restore-using-mysqldump-utility"></a>Sichern und Wiederherstellen mit dem mysqldump-Hilfsprogramm

### <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Erstellen einer Sicherungsdatei über die Befehlszeile „mysqldump“
Führen Sie den folgenden Befehl aus, um eine vorhandene MySQL-Datenbank auf dem lokalen Server oder einem virtuellen Computer zu sichern:
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Es müssen folgende Parameter bereitgestellt werden:
- [uname] Der Datenbankbenutzername
- [pass] Das Kennwort für Ihre Datenbank (beachten Sie, dass kein Leerzeichen zwischen „-p“ und dem Kennwort verwendet wird)
- [dbname] Der Name der Datenbank
- [backupfile.sql] Der Dateiname für die Datenbanksicherung
- [--opt] Die mysqldump-Option

Um eine Datenbank namens „testdb“ mit dem Benutzernamen „testuser“ auf Ihrem MySQL-Server ohne Kennwort als Datei mit dem Namen „testdb_backup.sql“ zu sichern, verwenden Sie folgenden Befehl. Mit dem Befehl wird die Datenbank `testdb` in einer Datei mit dem Namen `testdb_backup.sql` gesichert, die alle SQL-Anweisungen enthält, die zum erneuten Erstellen der Datenbank erforderlich sind. Stellen Sie sicher, dass der Benutzername „testuser“ mindestens über die SELECT-Berechtigung für verworfene Tabellen, die SHOW VIEW-Berechtigung für verworfene Sichten, die TRIGGER-Berechtigung für verworfene Trigger und die LOCK TABLES-Berechtigung verfügt, wenn die Option „--single-transaction“ nicht verwendet wird.

```bash
GRANT SELECT, LOCK TABLES, SHOW VIEW ON *.* TO 'testuser'@'hostname' IDENTIFIED BY 'password';
```
Führen Sie jetzt mysqldump aus, um die Sicherung der `testdb`-Datenbank zu erstellen.

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

### <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Wiederherstellen der MySQL-Datenbank über die Befehlszeile oder MySQL Workbench
Nachdem Sie die Zieldatenbank erstellt haben, können Sie mithilfe des Befehls „mysql“ oder von MySQL Workbench Daten in der jeweiligen neu erstellten Datenbank anhand der Sicherungsdatei wiederherstellen.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
In diesem Beispiel stellen Sie die Daten in der neu erstellten Datenbank auf dem Azure-Datenbank für MySQL-Server wieder her.

Hier ist ein Beispiel dafür, wie dieses **mysql** für **Single Server** verwendet wird:

```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```
Hier ist ein Beispiel dafür, wie dieses **mysql** für **Flexible Server** verwendet wird:

```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin -p testdb < testdb_backup.sql
```
---

## <a name="dump-and-restore-using-phpmyadmin"></a>Sichern und Wiederherstellen mithilfe von PHPMyAdmin
Befolgen Sie diese Schritte, um eine Datenbank mithilfe von PHPMyadmin zu sichern und wiederherzustellen.

> [!NOTE]
> Für Single Server muss der Benutzername im Format 'username@servername' vorliegen, aber für Flexible Server können Sie einfach 'Benutzername' verwenden. Wenn Sie 'username@servername' für Flexible Server verwenden, tritt ein Fehler bei der Verbindung auf.

### <a name="export-with-phpmyadmin"></a>Exportieren mit PHPMyadmin
Für den Export können Sie das allgemeine Tool phpMyAdmin verwenden, das Sie eventuell bereits lokal in Ihrer Umgebung installiert haben. So exportieren Sie Ihre MySQL-Datenbank mithilfe von PHPMyAdmin:
1. Öffnen Sie phpMyAdmin.
2. Wählen Sie Ihre Datenbank aus. Klicken Sie in der Liste links auf den Datenbanknamen.
3. Klicken Sie auf den Link **Export**. Eine neue Seite mit dem Speicherabbild der Datenbank wird angezeigt.
4. Klicken Sie im Bereich „Export“ auf den Link **Alle auswählen**, um die Tabellen in Ihrer Datenbank auszuwählen.
5. Klicken Sie im Bereich mit den SQL-Optionen auf die gewünschten Optionen.
6. Klicken Sie auf die Option **Als Datei speichern** und die entsprechende Komprimierungsoption. Klicken Sie dann auf die Schaltfläche **Gehe zu**. Es sollte ein Dialogfeld angezeigt werden, in dem Sie dazu aufgefordert werden, die Datei lokal zu speichern.

### <a name="import-using-phpmyadmin"></a>Importieren mithilfe von PHPMyAdmin
Der Import der Datenbank ist mit dem Exportvorgang vergleichbar. Führen Sie folgende Aktionen durch:
1. Öffnen Sie phpMyAdmin.
2. Klicken Sie auf der phpMyAdmin-Setupseite auf **Hinzufügen**, um Ihren Azure-Datenbank für MySQL-Server hinzuzufügen. Geben Sie die Verbindungsdetails und Anmeldeinformationen an.
3. Erstellen Sie eine passend benannte Datenbank, und wählen Sie sie links auf dem Bildschirm aus. Klicken Sie zum erneuten Generieren der vorhandenen Datenbank auf den Datenbanknamen, aktivieren Sie alle Kontrollkästchen neben den Tabellennamen, und wählen Sie **Drop** (Verwerfen), um die vorhandenen Tabellen zu löschen.
4. Klicken Sie auf den Link **SQL**, um die Seite anzuzeigen, auf der Sie SQL-Befehle eingeben oder Ihre SQL-Datei hochladen können.
5. Verwenden Sie die Schaltfläche **Durchsuchen**, um nach der Datenbankdatei zu suchen.
6. Klicken Sie auf die Schaltfläche **Go** (Los), um die Sicherung zu exportieren, die SQL-Befehle auszuführen und die Datenbank neu zu erstellen.

## <a name="known-issues"></a>Bekannte Probleme
Informationen zu bekannten Problemen, Tipps und Tricks finden Sie in unserem [TechCommunity-Blog](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/tips-and-tricks-in-using-mysqldump-and-mysql-restore-to-azure/ba-p/916912).

## <a name="next-steps"></a>Nächste Schritte
- [Herstellen einer Verbindung zwischen Anwendungen und Azure Database for MySQL](./howto-connection-string.md)
- Weitere Informationen zum Migrieren von Datenbanken zu Azure Database for MySQL finden Sie im [Leitfaden zur Datenbankmigration](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
- Wenn Sie große Datenbanken mit mehr als 1 TB migrieren möchten, können Sie die Verwendung von Communitytools wie **mydumper/myloader** in Erwägung ziehen, die den parallelen Export und Import unterstützen. Erfahren Sie mehr über das [Migrieren großer MySQL-Datenbanken](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699).
