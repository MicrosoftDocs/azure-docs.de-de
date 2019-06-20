---
title: Importieren und Exportieren in bzw. aus Azure Database for MySQL
description: In diesem Artikel werden gängige Möglichkeiten zum Importieren von Datenbanken in bzw. Exportieren von Datenbanken aus Azure-Datenbank für MySQL mithilfe von Tools wie MySQL Workbench erläutert.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/01/2018
ms.openlocfilehash: fa72037c8f54271f5651667765c5d5e2e9c03619
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60838072"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrieren der MySQL-Datenbank durch Import und Export
In diesem Artikel werden zwei gängige Vorgehensweisen beim Importieren von Daten zu bzw. Exportieren von Daten von einem Server für Azure-Datenbank für MySQL mithilfe von MySQL Workbench erläutert. 

## <a name="before-you-begin"></a>Voraussetzungen
Zum Ausführen der Schritte in dieser Anleitung benötigen Sie Folgendes:
- Einen Server für Azure-Datenbank für MySQL (befolgen Sie dazu die Anleitung in [Erstellen eines Servers für Azure-Datenbank für MySQL mithilfe des Azure-Portals](quickstart-create-mysql-server-database-using-azure-portal.md)).
- Sie müssen MySQL Workbench oder ein anderes MySQL-Tool zum Importieren und Exportieren[heruntergeladen](https://dev.mysql.com/downloads/workbench/) haben.

## <a name="use-common-tools"></a>Verwenden von gängigen Tools
Mithilfe von gängigen Tools wie MySQL Workbench, Toad oder Navicat können Sie eine Remoteverbindung zu Azure-Datenbank für MySQL herstellen und Daten importieren oder exportieren. 

Verwenden Sie diese Tools auf Ihrem Clientcomputer mit Internetverbindung, um eine Verbindung mit Azure-Datenbank für MySQL herzustellen. Verwenden Sie eine SSL-verschlüsselte Verbindung gemäß den bewährten Sicherheitsmethoden; eine Beschreibung finden Sie in [Konfigurieren von SSL-Konnektivität in Azure-Datenbank für MySQL](concepts-ssl-connection-security.md).

Bei der Migration auf Azure-Datenbank für MySQL müssen Sie Ihre Import- und Exportdateien nicht in einen bestimmten Cloudspeicherort verschieben. 

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Erstellen einer Datenbank auf dem Server für Azure-Datenbank für MySQL
Erstellen Sie eine leere Datenbank auf dem Server für Azure-Datenbank für MySQL, zu dem Sie die Daten migrieren möchten. Verwenden Sie ein Tool wie MySQL Workbench, Toad oder Navicat zum Erstellen der Datenbank. Die Datenbank kann denselben Namen aufweisen wie die Datenbank, die die Sicherungsdaten enthält, Sie können aber auch eine Datenbank mit einem anderen Namen erstellen.

Ermitteln Sie zum Herstellen der Verbindung die Verbindungsinformationen in der **Übersicht** Ihrer Azure Database for MySQL-Instanz.

![Ermitteln der Verbindungsinformationen im Azure-Portal](./media/concepts-migrate-import-export/1_server-overview-name-login.png)

Fügen Sie die Verbindungsinformationen in MySQL Workbench hinzu.

![MySQL Workbench-Verbindungszeichenfolge](./media/concepts-migrate-import-export/2_setup-new-connection.png)

## <a name="determine-when-to-use-import-and-export-techniques-instead-of-a-dump-and-restore"></a>Ermitteln der Fälle, in denen Import- und Exportverfahren anstelle von Sicherung und Wiederherstellung zu verwenden sind
Verwenden Sie in den folgenden Szenarien MySQL-Tools zum Importieren und Exportieren von Datenbanken in Azure MySQL-Datenbank. In anderen Szenarien empfiehlt es sich möglicherweise, den Ansatz [Sichern und Wiederherstellen](concepts-migrate-dump-restore.md) zu verfolgen. 

- Wenn Sie selektiv einige Tabellen zum Importieren aus einer vorhandenen MySQL-Datenbank in Azure MySQL-Datenbank auswählen, empfiehlt es sich, das Import- und Exportverfahren zu nutzen.  Damit können Sie nicht benötigte Tabellen aus der Migration ausschließen und auf diese Weise Zeit und Ressourcen sparen. Verwenden Sie beispielsweise den Schalter `--include-tables` oder `--exclude-tables` mit [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) und den Schalter `--tables` mit [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Wenn Sie andere Datenbankobjekte als Tabellen verschieben, müssen Sie diese Objekte explizit erstellen. Schließen Sie Einschränkungen (Primärschlüssel, Fremdschlüssel, Indizes), Sichten, Funktionen, Prozeduren, Trigger und andere Datenbankobjekte ein, die migriert werden sollen.
- Erstellen Sie beim Migrieren von Daten aus anderen externen Datenquellen als einer MySQL-Datenbank Flatfiles, und importieren Sie diese mithilfe von [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

Stellen Sie sicher, dass für alle Tabellen in der Datenbank die InnoDB-Speicher-Engine verwendet wird, wenn Sie Daten in Azure Database for MySQL laden. Azure Database for MySQL unterstützt nur die InnoDB-Speicher-Engine und bietet keine Unterstützung für alternative Speicher-Engines. Wenn Ihre Tabellen alternative Speicher-Engines erfordern, müssen Sie sie so konvertieren, dass sie das Format der InnoDB-Engine verwenden, ehe Sie die Migration zu Azure Database for MySQL ausführen. 

Angenommen, Sie verfügen über eine WordPress- oder Web-App, die die MyISAM-Engine verwendet. In diesem Fall müssen Sie erst die Tabellen konvertieren, indem Sie die Daten in InnoDB-Tabellen migrieren. Führen Sie anschließend eine Wiederherstellung in Azure-Datenbank für MySQL aus. Verwenden Sie die `ENGINE=INNODB`-Klausel, um die Engine festzulegen, mit der eine Tabelle erstellt wird, und übertragen Sie die Daten vor der Migration in die kompatible Tabelle. 

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

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Assistenten zum Exportieren und Importieren von Tabellendaten im Kontextmenü des Objektkatalogs
![MySQL Workbench-Assistenten im Kontextmenü des Objektkatalogs](./media/concepts-migrate-import-export/p1.png)

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
![Exportieren von Daten mit MySQL Workbench über den Navigatorbereich](./media/concepts-migrate-import-export/p2.png)

Sie können Ihre MySQL-Daten mithilfe der Registerkarte **Data Export** (Datenexport) exportieren. 
1. Wählen Sie jedes zu exportierende Schema aus, wählen Sie optional bestimmte Schemaobjekte/-tabellen aus einzelnen Schemas aus, und generieren Sie den Export. Zu den Konfigurationsoptionen zählen das Exportieren in einen Projektordner oder als eigenständige SQL-Datei, das Sichern von gespeicherten Routinen und Ereignissen oder das Überspringen von Tabellendaten. 
 
   Verwenden Sie alternativ die Option **Resultset exportieren**, um ein bestimmtes Resultset im SQL-Editor in einem anderen Format (z.B. CSV, JSON, HTML, XML) zu exportieren. 
3. Wählen Sie die zu exportierenden Datenbankobjekte aus, und konfigurieren Sie die entsprechenden Optionen.
4. Klicken Sie auf **Aktualisieren**, um die aktuellen Objekte zu laden.
5. Öffnen Sie optional die Registerkarte **Erweiterte Optionen**, mit der Sie den Exportvorgang verfeinern können. Fügen Sie beispielsweise Tabellensperren hinzu, und verwenden Sie Replace- anstelle von Insert-Anweisungen sowie Anführungszeichner-Kennzeichner mit Backtick-Zeichen.
6. Klicken Sie auf **Export starten**, um den Exportvorgang zu beginnen.


#### <a name="data-import"></a>Datenimport
![Importieren von Daten mit MySQL Workbench über den Verwaltungsnavigator](./media/concepts-migrate-import-export/p3.png)

Über die Registerkarte **Data Import** (Datenimport) können Sie im Rahmen des Datenexportvorgangs oder mit dem Befehl „mysqldump“ exportierte Daten importieren oder wiederherstellen. 
1. Wählen Sie den Projektordner oder die eigenständige SQL-Datei aus, wählen Sie das Schema aus, in das importiert werden soll, oder wählen Sie **Neu** aus, um ein neues Schema zu definieren. 
2. Klicken Sie auf **Import beginnen** , um den Importvorgang zu starten.

## <a name="next-steps"></a>Nächste Schritte
- Ein weiterer Migrationsansatz wird im Artikel [Migrieren der MySQL-Datenbank durch Sicherungen und Wiederherstellungen in Azure-Datenbank für MySQL](concepts-migrate-dump-restore.md) beschrieben.
- Weitere Informationen zum Migrieren von Datenbanken zu Azure Database for MySQL finden Sie im [Leitfaden zur Datenbankmigration](https://aka.ms/datamigration). 
