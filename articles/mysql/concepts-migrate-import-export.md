---
title: Importieren und Exportieren – Azure Database for MySQL
description: In diesem Artikel werden gängige Möglichkeiten zum Importieren von Datenbanken in bzw. Exportieren von Datenbanken aus Azure-Datenbank für MySQL mithilfe von Tools wie MySQL Workbench erläutert.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/30/2020
ms.openlocfilehash: af9f0f65e01a786d478fac0adde6174b8f03b2fd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019894"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrieren der MySQL-Datenbank durch Import und Export
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]
In diesem Artikel werden zwei gängige Vorgehensweisen beim Importieren von Daten zu bzw. Exportieren von Daten von einem Server für Azure-Datenbank für MySQL mithilfe von MySQL Workbench erläutert.

Ausführliche Informationen und Anwendungsfälle für die Migration von Datenbanken zu Azure Database for MySQL finden Sie auch im [Leitfaden zur Datenbankmigration](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide). Dieser Leitfaden enthält wichtige Informationen zur erfolgreichen Planung und Ausführung einer MySQL-Migration zu Azure.

## <a name="before-you-begin"></a>Voraussetzungen
Zum Ausführen der Schritte in dieser Anleitung benötigen Sie Folgendes:
- Einen Server für Azure-Datenbank für MySQL (befolgen Sie dazu die Anleitung in [Erstellen eines Servers für Azure-Datenbank für MySQL mithilfe des Azure-Portals](quickstart-create-mysql-server-database-using-azure-portal.md)).
- [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) oder ein anderes MySQL-Drittanbietertool für den Import/Export.

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Erstellen einer Datenbank auf dem Server für Azure-Datenbank für MySQL
Erstellen Sie eine leere Datenbank auf dem Azure Database for MySQL-Server mithilfe von MySQL Workbench, Toad oder Navicat, um die Datenbank zu erstellen. Die Datenbank kann denselben Namen aufweisen wie die Datenbank, die die Sicherungsdaten enthält, Sie können aber auch eine Datenbank mit einem anderen Namen erstellen.

Ermitteln Sie zum Herstellen der Verbindung die Verbindungsinformationen in der **Übersicht** Ihrer Azure Database for MySQL-Instanz.

:::image type="content" source="./media/concepts-migrate-import-export/1_server-overview-name-login.png" alt-text="Ermitteln der Verbindungsinformationen im Azure-Portal":::

Fügen Sie die Verbindungsinformationen in MySQL Workbench hinzu.

:::image type="content" source="./media/concepts-migrate-import-export/2_setup-new-connection.png" alt-text="MySQL Workbench-Verbindungszeichenfolge":::

## <a name="determine-when-to-use-import-and-export-techniques"></a>Bestimmen der Verwendung von Import- und Exportverfahren

> [!TIP]
> Für Szenarien, in denen Sie die gesamte Datenbank sichern und wiederherstellen möchten, sollten Sie stattdessen den Ansatz [Sichern und Wiederherstellen](concepts-migrate-dump-restore.md) verwenden.

Verwenden Sie in den folgenden Szenarien MySQL-Tools zum Importieren und Exportieren von Datenbanken in Azure MySQL-Datenbank.

- Wenn Sie selektiv einige Tabellen zum Importieren aus einer vorhandenen MySQL-Datenbank in Azure MySQL-Datenbank auswählen, empfiehlt es sich, das Import- und Exportverfahren zu nutzen.  Damit können Sie nicht benötigte Tabellen aus der Migration ausschließen und auf diese Weise Zeit und Ressourcen sparen. Verwenden Sie beispielsweise den Schalter `--include-tables` oder `--exclude-tables` mit [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) und den Schalter `--tables` mit [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Wenn Sie andere Datenbankobjekte als Tabellen verschieben, müssen Sie diese Objekte explizit erstellen. Schließen Sie Einschränkungen (Primärschlüssel, Fremdschlüssel, Indizes), Sichten, Funktionen, Prozeduren, Trigger und andere Datenbankobjekte ein, die migriert werden sollen.
- Erstellen Sie beim Migrieren von Daten aus anderen externen Datenquellen als einer MySQL-Datenbank Flatfiles, und importieren Sie diese mithilfe von [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

> [!Important]
> Sowohl Single Server als auch Flexible Server unterstützen **nur die InnoDB-Speicher-Engine**. Stellen Sie sicher, dass für alle Tabellen in der Datenbank die InnoDB-Speicher-Engine verwendet wird, wenn Sie Daten in Azure Database for MySQL laden.
> Wenn Ihre Quelldatenbank eine andere Speicher-Engine verwendet, konvertieren Sie sie vor der Migration der Datenbank in die InnoDB-Engine. Angenommen, Sie verfügen über eine WordPress- oder Web-App, die die MyISAM-Engine verwendet. In diesem Fall müssen Sie erst die Tabellen konvertieren, indem Sie die Daten in InnoDB-Tabellen migrieren. Verwenden Sie die `ENGINE=INNODB`-Klausel, um die Engine festzulegen, mit der eine Tabelle erstellt wird, und übertragen Sie die Daten vor der Migration in die kompatible Tabelle.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Leistungsbezogene Empfehlungen für Import und Export
-   Erstellen Sie vor dem Laden von Daten gruppierte Indizes und Primärschlüssel. Laden Sie Daten in der Reihenfolge des Primärschlüssels.
-   Verzögern Sie die Erstellung sekundärer Indizes, bis die Daten vollständig geladen wurden. Erstellen Sie alle sekundären Indizes nach dem Laden.
-   Deaktivieren Sie vor dem Laden Fremdschlüsseleinschränkungen. Das Deaktivieren von Fremdschlüsselüberprüfungen führt zu erheblichen Leistungssteigerungen. Aktivieren Sie die Einschränkungen, und überprüfen Sie die Daten nach dem Laden, um die referentielle Integrität sicherzustellen.
-   Laden Sie Daten parallel. Vermeiden Sie zu viel Parallelität, da dies zum Erreichen eines Ressourcenlimits führen kann, und überwachen Sie die Ressourcen mit den Metriken, die im Azure-Portal verfügbar sind.
-   Verwenden Sie ggf. partitionierte Tabellen.

## <a name="import-and-export-by-using-mysql-workbench"></a>Importieren und Exportieren mit MySQL Workbench
Es gibt zwei Möglichkeiten für den Export und Import von Daten in MySQL Workbench. Jede dient jeweils zu einem anderen Zweck.

> [!NOTE]
> Wenn Sie eine Verbindung mit dem MySQL Single oder Flexible Server (Vorschau) für MySQL Workbench hinzufügen, stellen Sie Folgendes sicher:
> - Für MySQL Single Server muss der Benutzername das Format 'username@servername' aufweisen.
> - Für MySQL Flexible Server können Sie einfach 'Benutzername' verwenden. Wenn Sie 'username@servername' zum Herstellen der Verbindung verwenden, tritt ein Fehler bei der Verbindung auf.

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Assistenten zum Exportieren und Importieren von Tabellendaten im Kontextmenü des Objektkatalogs
:::image type="content" source="./media/concepts-migrate-import-export/p1.png" alt-text="MySQL Workbench-Assistenten im Kontextmenü des Objektkatalogs":::

Die Assistenten für Tabellendaten unterstützen Import- und Exportvorgänge unter Verwendung von CSV- und JSON-Dateien. Sie bieten eine Reihe von Konfigurationsoptionen, beispielsweise Trennzeichen, Spaltenauswahl und Codierungsauswahl. Sie können jeden Assistenten für lokal oder remote verbundene MySQL-Server ausführen. Die Importaktion schließt die Tabellen-, Spalten- und Typzuordnung ein.

Sie können auf diese Assistenten über das Kontextmenü des Objektkatalogs zugreifen; dieses rufen Sie auf, indem Sie mit der rechten Maustaste auf eine Tabelle klicken. Wählen Sie anschließend entweder **Assistent zum Exportieren von Tabellendaten** oder **Assistent zum Importieren von Tabellendaten** aus.

#### <a name="table-data-export-wizard"></a>Assistent zum Exportieren von Tabellendaten
Im folgenden Beispiel wird die Tabelle in eine CSV-Datei exportiert:
1. Klicken Sie mit der rechten Maustaste auf die Tabelle der zu exportierenden Datenbank.
2. Wählen Sie **Assistent zum Exportieren von Tabellendaten** aus. Wählen Sie die zu exportierenden Spalten, den Zeilenoffset (sofern vorhanden) und die Anzahl (sofern vorhanden) aus.
3. Klicken Sie auf der Seite **Auswählen der zu exportierenden Daten** auf **Weiter**. Wählen Sie den Dateipfad sowie den Dateityp CSV oder JSON aus. Wählen Sie darüber hinaus das Zeilentrennzeichen, die Methode zum Einschließen von Zeichenfolgen sowie das Feldtrennzeichen aus.
4. Klicken Sie auf der Seite **Select output file location** (Speicherort der Ausgabedatei auswählen) auf **Weiter**.
5. Klicken Sie auf der Seite **Daten exportieren** auf **Weiter**.

#### <a name="table-data-import-wizard"></a>Assistent zum Importieren von Tabellendaten
Im folgenden Beispiel wird die Tabelle aus einer CSV-Datei importiert:
1. Klicken Sie mit der rechten Maustaste auf die Tabelle der zu importierenden Datenbank.
2. Navigieren Sie zu der zu importierenden CSV-Datei, und wählen Sie sie aus. Klicken Sie anschließend auf **Weiter**.
3. Wählen Sie die Zieltabelle (neu oder vorhanden) aus, und aktivieren oder deaktivieren Sie das Kontrollkästchen **Truncate table before import** (Tabelle vor Import abschneiden). Klicken Sie auf **Weiter**.
4. Wählen Sie die Codierung und die zu importierenden Spalten aus, und klicken Sie auf **Weiter**.
5. Klicken Sie auf der Seite **Daten importieren** auf **Weiter**. Der Assistent importiert die Daten entsprechend den ausgewählten Einstellungen.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Assistent zum Exportieren und Importieren von SQL-Daten im Navigatorbereich
Exportieren oder importieren Sie mit einem Assistenten SQL-Daten, die von MySQL Workbench oder mit dem Befehl „mysqldump“ generiert wurden. Sie können diese Assistenten im **Navigatorbereich** aufrufen, oder indem Sie im Hauptmenü die Option **Server** auswählen. Wählen Sie anschließend **Data Export** (Datenexport) oder **Data Import** (Datenimport) aus.

#### <a name="data-export"></a>Datenexport
:::image type="content" source="./media/concepts-migrate-import-export/p2.png" alt-text="Exportieren von Daten mit MySQL Workbench über den Navigatorbereich":::

Sie können Ihre MySQL-Daten mithilfe der Registerkarte **Data Export** (Datenexport) exportieren.
1. Wählen Sie jedes zu exportierende Schema aus, wählen Sie optional bestimmte Schemaobjekte/-tabellen aus einzelnen Schemas aus, und generieren Sie den Export. Zu den Konfigurationsoptionen zählen das Exportieren in einen Projektordner oder als eigenständige SQL-Datei, das Sichern von gespeicherten Routinen und Ereignissen oder das Überspringen von Tabellendaten.

   Verwenden Sie alternativ die Option **Resultset exportieren**, um ein bestimmtes Resultset im SQL-Editor in einem anderen Format (z.B. CSV, JSON, HTML, XML) zu exportieren.
3. Wählen Sie die zu exportierenden Datenbankobjekte aus, und konfigurieren Sie die entsprechenden Optionen.
4. Klicken Sie auf **Aktualisieren**, um die aktuellen Objekte zu laden.
5. Öffnen Sie optional die Registerkarte **Erweiterte Optionen**, mit der Sie den Exportvorgang verfeinern können. Fügen Sie beispielsweise Tabellensperren hinzu, und verwenden Sie Replace- anstelle von Insert-Anweisungen sowie Anführungszeichner-Kennzeichner mit Backtick-Zeichen.
6. Klicken Sie auf **Export starten**, um den Exportvorgang zu beginnen.


#### <a name="data-import"></a>Datenimport
:::image type="content" source="./media/concepts-migrate-import-export/p3.png" alt-text="Importieren von Daten mit MySQL Workbench über den Verwaltungsnavigator":::

Über die Registerkarte **Data Import** (Datenimport) können Sie im Rahmen des Datenexportvorgangs oder mit dem Befehl „mysqldump“ exportierte Daten importieren oder wiederherstellen.
1. Wählen Sie den Projektordner oder die eigenständige SQL-Datei aus, wählen Sie das Schema aus, in das importiert werden soll, oder wählen Sie **Neu** aus, um ein neues Schema zu definieren.
2. Klicken Sie auf **Import beginnen** , um den Importvorgang zu starten.

## <a name="next-steps"></a>Nächste Schritte
- Ein weiterer Migrationsansatz wird im Artikel [Migrieren der MySQL-Datenbank durch Sicherungen und Wiederherstellungen in Azure-Datenbank für MySQL](concepts-migrate-dump-restore.md) beschrieben.
- Weitere Informationen zum Migrieren von Datenbanken zu Azure Database for MySQL finden Sie im [Leitfaden zur Datenbankmigration](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).