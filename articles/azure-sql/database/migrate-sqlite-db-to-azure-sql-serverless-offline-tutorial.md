---
title: 'Tutorial: Migrieren Ihrer SQLite-Datenbank zu „Azure SQL-Datenbank – serverlos“'
description: Erfahren Sie, wie Sie mithilfe von Azure Data Factory eine Offlinemigration von SQLite zu „Azure SQL-Datenbank – serverlos“ durchführen.
services: sql-database
author: joplum
ms.author: joplum
ms.service: sql-database
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/08/2020
ms.custom: sqldbrb=1
ms.openlocfilehash: e2f240247cbba0f80254d504792df45be55c6a1b
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790405"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>Migrieren Ihrer SQLite-Datenbank zu „Azure SQL-Datenbank – serverlos“
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Für viele bildet SQLite den Einstieg in die Welt der Datenbanken und der SQL-Programmierung. Dank der Integration in zahlreiche Betriebssysteme und beliebte Anwendungen ist SQLite eine der am weitesten verbreiteten und meistgenutzten Datenbankengines der Welt. Und da es für viele Benutzer die erste Datenbankengine ist, ist sie häufig ein zentraler Bestandteil von Projekten und Anwendungen. In Fällen, in denen das Projekt oder die Anwendung über die ursprüngliche SQLite-Implementierung hinauswächst, müssen Entwickler ihre Daten ggf. zu einem zuverlässigen, zentralisierten Datenspeicher migrieren.

„Azure SQL-Datenbank – serverlos“ ist ein Computetarif für Einzeldatenbanken, bei dem Computeressourcen basierend auf dem Workloadbedarf automatisch skaliert werden und die Nutzung dieser Ressourcen sekundengenau abrechnet wird. Wenn nur der verwendete Speicher in Rechnung gestellt wird, hält die serverlose Computeebene außerdem Datenbanken während inaktiver Zeiträume automatisch an und startet diese wieder, wenn es wieder zu Aktivität kommt.

Mit den im Anschluss aufgeführten Schritten wird Ihre Datenbank mit nur einigen geringfügigen Änderungen am Anwendungscode zu „Azure SQL-Datenbank – serverlos“ migriert, sodass Sie sie anderen Benutzern oder Anwendungen in der Cloud zur Verfügung stellen können und lediglich für die tatsächliche Nutzung bezahlen.

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement
- Zu migrierende SQLite2- oder SQLite3-Datenbank
- Windows-Umgebung
  - Sollten Sie über keine lokale Windows-Umgebung verfügen, können Sie für die Migration einen virtuellen Windows-Computer in Azure verwenden. Verschieben Sie Ihre SQLite-Datenbankdatei, und machen Sie sie mithilfe von Azure Files und Storage-Explorer auf dem virtuellen Computer verfügbar.

## <a name="steps"></a>Schritte

1. Stellen Sie eine neue Azure SQL-Datenbank auf der serverlosen Computeebene bereit.

    ![Screenshot: Azure-Portal mit Bereitstellungsbeispiel für „Azure SQL-Datenbank – serverlos“](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/provision-serverless.png)

2. Vergewissern Sie sich, dass Ihre SQLite-Datenbankdatei in Ihrer Windows-Umgebung verfügbar ist. Installieren Sie einen SQLite-ODBC-Treiber, sofern noch keiner vorhanden ist. (Hierzu stehen zahlreiche Open-Source-Optionen zur Verfügung – beispielsweise http://www.ch-werner.de/sqliteodbc/) ).

3. Erstellen Sie einen System-DSN für die Datenbank. Verwenden Sie die passende Datenquellenadministrator-Anwendung für Ihre Systemarchitektur (32 Bit oder 64 Bit). Die von Ihnen verwendete Version können Sie anhand der Systemeinstellungen ermitteln.

    - Öffnen Sie „ODBC-Datenquellenadministrator“ in Ihrer Umgebung.
    - Klicken Sie auf die Registerkarte „System-DSN“ und anschließend auf „Hinzufügen“.
    - Wählen Sie den von Ihnen installierten SQLite-ODBC-Connector aus, und geben Sie der Verbindung einen aussagekräftigen Namen (beispielsweise „sqlitemigrationsource“).
    - Legen Sie den Datenbanknamen auf die DB-Datei fest.
    - Speichern Sie Ihre Angaben, und schließen Sie die Anwendung.

4. Laden Sie die selbstgehostete Integration Runtime herunter, und installieren Sie sie. Das geht am einfachsten mit der Express-Installationsoption. (Eine ausführliche Beschreibung finden Sie in der Dokumentation.) Wenn Sie sich für eine manuelle Installation entscheiden, müssen Sie einen Authentifizierungsschlüssel für die Anwendung bereitstellen. Dieser kann sich in Ihrer Data Factory-Instanz befinden. Gehen Sie hierzu wie folgt vor:

    - Starten Sie ADF, indem Sie im Azure-Portal unter dem Dienst auf „Erstellen und überwachen“ klicken.
    - Klicken Sie auf der linken Seite auf die Registerkarte „Erstellen“ (blauer Stift).
    - Klicken Sie links unten auf „Verbindungen“ und anschließend auf „Integration Runtimes“.
    - Fügen Sie eine neue selbstgehostete Integration Runtime hinzu, geben Sie einen Namen an, und wählen Sie *Option 2* aus.

5. Erstellen Sie einen neuen verknüpften Dienst für die SQLite-Quelldatenbank in Ihrer Data Factory-Instanz.

    ![Screenshot: Leeres Blatt „Verknüpfte Dienste“ in Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-create.png)

6. Klicken Sie bei **Verbindungen** unter **Verknüpfter Dienst** auf **Neu** .

7. Suchen Sie nach dem Connector „ODBC“, und wählen Sie ihn aus.

   ![Screenshot: Blatt „Verknüpfte Dienste“ in Azure Data Factory mit Logo des ODBC-Connectors](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-odbc.png)

8. Geben Sie dem verknüpften Dienst einen aussagekräftigen Namen (beispielsweise „sqlite_odbc“). Wählen Sie in der Dropdownliste „Connect via integration runtime“ (Verbindung per Integration Runtime herstellen) Ihre Integration Runtime aus. Geben Sie Folgendes als Verbindungszeichenfolge ein, und ersetzen Sie dabei die Variable „Initial Catalog“ durch den Dateipfad für die DB-Datei und den DSN durch den Namen der System-DSN-Verbindung:

   ```
   Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. Legen Sie den Authentifizierungstyp auf „Anonym“ fest.

10. Testen der Verbindung

    ![Screenshot: Erfolgreiche Verbindungsherstellung in Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-test-successful.png)

11. Erstellen Sie einen weiteren verknüpften Dienst für Ihr serverloses SQL-Ziel. Wählen Sie die Datenbank mithilfe des Assistenten für verknüpfte Dienste aus, und geben Sie die Anmeldeinformationen für die SQL-Authentifizierung an.

    ![Screenshot: Auswahl von Azure SQL-Datenbank in Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-create-target.png)

12. Extrahieren Sie die CREATE TABLE-Anweisungen aus Ihrer SQLite-Datenbank. Hierzu können Sie das folgende Python-Skript für Ihre Datenbankdatei ausführen:

    ```
    #!/usr/bin/python
    import sqlite3
    conn = sqlite3.connect("sqlitemigrationsource.db")
    c = conn.cursor()

    print("Starting extract job..")
    with open('CreateTables.sql', 'w') as f:
        for tabledetails in c.execute("SELECT * FROM sqlite_master WHERE type='table'"):
            print("Extracting CREATE statement for " + (str(tabledetails[1])))
            print(tabledetails)
            f.write(str(tabledetails[4].replace('\n','') + ';\n'))
    c.close()
    ```

13. Erstellen Sie die Zieltabellen in Ihrer serverlosen SQL-Zielumgebung, indem Sie die CREATE TABLE-Anweisungen aus der Datei „CreateTables.sql“ kopieren und die SQL-Anweisungen im Abfrage-Editor im Azure-Portal ausführen.

14. Kehren Sie zum Startbildschirm Ihrer Data Factory-Instanz zurück, und klicken Sie auf „Daten kopieren“, um den Auftragserstellungs-Assistenten zu durchlaufen.

    ![Screenshot: Logo des Assistenten zum Kopieren von Daten in Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/copy-data.png)

15. Wählen Sie mithilfe der Kontrollkästchen alle Tabellen aus der SQLite-Quelldatenbank aus, und ordnen Sie sie den Zieltabellen in Azure SQL zu. Nach Ausführung des Auftrags haben Sie Ihre Daten erfolgreich von SQLite zu Azure SQL migriert.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu den ersten Schritten finden Sie unter [Schnellstart: Erstellen einer Einzeldatenbank in Azure SQL-Datenbank über das Azure-Portal](single-database-create-quickstart.md).
- Ressourceneinschränkungen werden unter [Ressourceneinschränkungen für die serverlose Computeebene](./resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5) beschrieben.