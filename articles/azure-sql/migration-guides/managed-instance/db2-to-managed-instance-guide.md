---
title: 'Migrationsleitfaden: Db2 zu SQL Managed Instance'
description: Befolgen Sie diese Anleitung, um Ihre Db2-Datenbanken zu Azure SQL Managed Instance zu migrieren.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: c31187e87f18f0ec84cd5b80ccab997d0ba381c3
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96324753"
---
# <a name="migration-guide-db2-to-sql-managed-instance"></a>Migrationsleitfaden: Db2 zu SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

In diesem Leitfaden erfahren Sie, wie Sie Ihre Db2-Datenbanken mithilfe von SQL Server Migration Assistant für Db2 zu Azure SQL Managed Instance migrieren. 

Informationen zu anderen Szenarios finden Sie im [Leitfaden zur Datenbankmigration](https://datamigration.microsoft.com/).

## <a name="prerequisites"></a>Voraussetzungen 

Für die Migration Ihrer Db2-Datenbank zu SQL Managed Instance gelten folgende Voraussetzungen:

- Überprüfen Sie, ob Ihre Quellumgebung unterstützt wird.
- Laden Sie [SQL Server Migration Assistant (SSMA) for DB2](https://www.microsoft.com/download/details.aspx?id=54254) herunter.
- Sie benötigen eine [Azure SQL Managed Instance](../../database/single-database-create-quickstart.md)-Zielinstanz.


## <a name="pre-migration"></a>Vor der Migration

Wenn diese Voraussetzungen erfüllt sind, können Sie die Topologie Ihrer Umgebung ermitteln und die Durchführbarkeit der Migration bewerten. 

### <a name="assess-and-convert"></a>Bewerten und Konvertieren

Erstellen Sie mit SQL Server Migration Assistant (SSMA) eine Bewertung. 

Führen Sie die folgenden Schritte aus, um eine Bewertung zu erstellen:

1. Öffnen Sie SQL Server Migration Assistant (SSMA) for DB2. 
1. Klicken Sie auf **File** (Datei) und dann auf **New Project** (Neues Projekt). 
1. Geben Sie einen Projektnamen und einen Speicherort für das Projekt an, und wählen Sie dann Azure SQL Managed Instance in der Dropdownliste als Migrationsziel aus. Klicken Sie auf **OK**. 

   :::image type="content" source="media/db2-to-managed-instance-guide/new-project.png" alt-text="Geben Sie die Projektdetails an, und klicken Sie zum Speichern auf „OK“.":::


1. Geben Sie im Dialogfeld **Connect to DB2** (Mit DB2 verbinden) die Werte für die DB2-Verbindungsdetails ein. 

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-db2.png" alt-text="Stellen Sie eine Verbindung mit Ihrer DB2-Instanz her.":::


1. Klicken Sie mit der rechten Maustaste auf das DB2-Schema, das Sie migrieren möchten, und klicken Sie dann auf **Create report** (Bericht erstellen). Dadurch wird ein HTML-Bericht generiert. Alternativ können Sie nach dem Auswählen des Schemas in der Navigationsleiste auf **Create report** (Bericht erstellen) klicken. 

   :::image type="content" source="media/db2-to-managed-instance-guide/create-report.png" alt-text="Klicken Sie mit der rechten Maustaste auf das Schema, und klicken Sie dann auf „Create report“ (Bericht erstellen).":::

1. Sehen Sie sich den HTML-Bericht an, um die Konvertierungsstatistiken und etwaige Fehler oder Warnungen zu verstehen. Sie können den Bericht auch in Excel öffnen, um ein Inventar der DB2-Objekte sowie Informationen zum für die Durchführung von Schemakonvertierungen erforderlichen Aufwand zu erhalten. Der Standardspeicherort für den Bericht ist der Berichtsordner in SSMAProjects.

   Beispiel: `drive:\<username>\Documents\SSMAProjects\MyDB2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-managed-instance-guide/report.png" alt-text="Sehen Sie sich den Bericht an, um etwaige Fehler oder Warnungen zu identifizieren.":::


### <a name="validate-data-types"></a>Überprüfen von Datentypen

Überprüfen Sie die standardmäßig festgelegten Datentypzuordnungen, und ändern Sie sie bei Bedarf basierend auf den Anforderungen. Gehen Sie dazu folgendermaßen vor: 

1. Klicken Sie im Menü auf **Tools**. 
1. Klicken auf **Project Settings** (Projekteinstellungen). 
1. Klicken Sie auf die Registerkarte **Type Mappings** (Typzuordnungen). 

   :::image type="content" source="media/db2-to-managed-instance-guide/type-mapping.png" alt-text="Wählen Sie das Schema aus, und klicken Sie dann auf „Type Mappings“ (Typzuordnungen).":::

1. Sie können die Typzuordnung für die einzelnen Tabellen ändern, indem Sie die gewünschte Tabelle im **DB2 Metadata Explorer** (DB2-Metadaten-Explorer) auswählen. 

### <a name="schema-conversion"></a>Schemakonvertierung 

Führen Sie die folgenden Schritte aus, um das Schema zu konvertieren:

1. (Optional:) Fügen Sie Anweisungen dynamische Abfragen oder Ad-hoc-Abfragen hinzu. Klicken Sie mit der rechten Maustaste auf den Knoten, und klicken Sie dann auf **Add statements** (Anweisungen hinzufügen). 
1. Klicken Sie auf **Connect to Azure SQL Database** (Verbindung mit Azure SQL-Datenbank herstellen). 
    1. Geben Sie Verbindungsdetails ein, um eine Verbindung mit Ihrer Instanz von Azure SQL Managed Instance herzustellen.  
    1. Wählen Sie Ihre Zieldatenbank aus der Dropdownliste aus. 
    1. Wählen Sie **Verbinden** aus. 

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-sql-managed-instance.png" alt-text="Geben Sie die Details an, um eine Verbindung mit SQL Server herstellen.":::


1. Klicken Sie mit der rechten Maustaste auf das Schema, und klicken Sie dann auf **Convert Schema** (Schema konvertieren). Alternativ können Sie nach dem Auswählen des Schemas in der oberen Navigationsleiste auf **Convert Schema** (Schema konvertieren) klicken. 

   :::image type="content" source="media/db2-to-managed-instance-guide/convert-schema.png" alt-text="Klicken Sie mit der rechten Maustaste auf das Schema, und klicken Sie dann auf „Convert Schema“ (Schema konvertieren).":::

1. Vergleichen Sie nach Abschluss der Konvertierung die neue Schemastruktur mit der alten, und überprüfen Sie sie, um mögliche Probleme zu identifizieren und basierend auf den Empfehlungen zu behandeln. 

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-review-schema-structure.png" alt-text="Vergleichen Sie die neue Schemastruktur mit der alten, und überprüfen Sie sie, um mögliche Probleme zu identifizieren und basierend auf Empfehlungen zu behandeln.":::

1. Speichern Sie das Projekt für eine Übung zur Offlineschemakorrektur lokal. Klicken Sie im Menü **File** (Datei) auf **Save Project** (Projekt speichern). 


## <a name="migrate"></a>Migrate

Wenn Sie die Bewertung Ihrer Datenbanken und die Behandlung etwaiger Abweichungen abgeschlossen haben, besteht der nächste Schritt in der Durchführung des Migrationsprozesses.

Führen Sie die folgenden Schritte aus, um das Schema zu veröffentlichen und Ihre Daten zu migrieren:

1. Veröffentlichen des Schemas: Klicken Sie im **Metadaten-Explorer von Azure SQL Managed Instance** im Knoten **Datenbanken** mit der rechten Maustaste auf die Datenbank, und klicken Sie dann auf **Synchronize with Database** (Mit Datenbank synchronisieren).

   :::image type="content" source="media/db2-to-managed-instance-guide/synchronize-with-database.png" alt-text="Klicken Sie mit der rechten Maustaste auf die Datenbank, und klicken Sie dann auf „Synchronize with Database“ (Mit Datenbank synchronisieren).":::

1. Migrieren der Daten: Klicken Sie im **DB2 Metadata Explorer** (DB2-Metadaten-Explorer) mit der rechten Maustaste auf das Schema, und klicken Sie dann auf **Migrate Data** (Daten migrieren). 

   :::image type="content" source="media/db2-to-managed-instance-guide/migrate-data.png" alt-text="Klicken Sie mit der rechten Maustaste auf das Schema, und klicken Sie dann auf „Migrate Data“ (Daten migrieren).":::

1. Stellen Sie die Verbindungsdetails für Db2 und SQL Managed Instance bereit. 
1. Zeigen Sie den **Datenmigrationsbericht** an. 

   :::image type="content" source="media/db2-to-managed-instance-guide/data-migration-report.png" alt-text="Überprüfen Sie den Datenmigrationsbericht.":::

1. Stellen Sie über das SQL Server Management Studio eine Verbindung mit Ihrer Instanz von SQL Managed Instance her, und überprüfen Sie die Migration durch Überprüfen der Daten und des Schemas. 

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-schema-in-ssms.png" alt-text="Führen Sie einen Schemavergleich im SSMS durch.":::

## <a name="post-migration"></a>Nach der Migration 

Nach dem erfolgreichen Abschluss der Migrationsphase müssen Sie eine Reihe von Aufgaben nach der Migration ausführen, um sicherzustellen, dass alles so reibungslos und effizient wie möglich funktioniert.

### <a name="remediate-applications"></a>Korrigieren von Anwendungen 

Wenn die Daten in die Zielumgebung migriert wurden, müssen alle Anwendungen, die zuvor die Quelle verwendet haben, beginnen das Ziel zu verwenden. Hierfür sind in einigen Fällen Änderungen an den Anwendungen erforderlich.


### <a name="perform-tests"></a>Durchführen von Tests

Das Testvorgehen für die Datenbankmigration umfasst die folgenden Aktivitäten:

1. **Entwickeln von Validierungstests**: Für das Testen der Datenbankmigration müssen Sie SQL-Abfragen verwenden. Sie müssen die Validierungsabfragen erstellen, die für die Quell- und die Zieldatenbank ausgeführt werden sollen. Ihre Validierungsabfragen sollten den von Ihnen definierten Bereich abdecken.
1. **Einrichten der Testumgebung**: Die Testumgebung sollte eine Kopie der Quelldatenbank und der Zieldatenbank enthalten. Stellen Sie sicher, dass Sie die Testumgebung isolieren.
1. **Ausführen der Validierungstests**: Führen Sie die Validierungstests für die Quelle und das Ziel aus, und analysieren Sie anschließend die Ergebnisse.
1. **Ausführen von Leistungstests**: Führen Sie Leistungstests für die Quelle und das Ziel aus, und analysieren und vergleichen Sie anschließend die Ergebnisse.

   > [!NOTE]
   > Für Unterstützung bei der Entwicklung und Ausführung von Validierungstests nach der Migration sollten Sie die Datenqualitätslösung des Partners [QuerySurge](https://www.querysurge.com/company/partners/microsoft) in Erwägung ziehen. 


## <a name="leverage-advanced-features"></a>Nutzen der erweiterten Features 

Sie sollten unbedingt die erweiterten cloudbasierten Features von Azure SQL Managed Instance nutzen, z. B. die [integrierte Hochverfügbarkeit](../../database/high-availability-sla.md), die [Bedrohungserkennung](../../database/azure-defender-for-sql.md) und die [Überwachung und Optimierung Ihrer Workloads](../../database/monitor-tune-overview.md). 


Einige SQL Server-Features sind nur verfügbar, wenn der [Datenbank-Kompatibilitätsgrad](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) in den neuesten Kompatibilitätsgrad (150) geändert wurde. 

## <a name="migration-assets"></a>Migrationsressourcen 

Weitere Unterstützung erhalten Sie in Form der folgenden Ressourcen, die im Rahmen eines Auftrags für ein echtes Migrationsprojekt entwickelt wurden:

|Asset  |BESCHREIBUNG  |
|---------|---------|
|[Data Workload Assessment Model and Tool (Datenarbeitsauslastungs-Bewertungsmodell und -tool)](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Dieses Tool stellt für eine angegebene Arbeitsauslastung Informationen zu empfohlenen optimalen Zielplattformen, zur Cloudbereitschaft und zum Korrekturbedarf für Anwendungen/Datenbanken bereit. Es bietet eine einfache Berechnung und Berichterstellung mit nur einem Klick, die Ihnen durch Bereitstellung eines automatisierten und einheitlichen Zielplattform-Entscheidungsprozesses dabei helfen, umfangreiche Bewertungen zu beschleunigen.|
|[DB2 z/OS Data Assets Discovery and Assessment Package (Paket zur Ermittlung und Bewertung von DB2 z/OS-Datenressourcen)](https://github.com/Microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Nach dem Ausführen des SQL-Skripts für eine-Datenbank können Sie die Ergebnisse in eine Datei im Dateisystem exportieren. Es werden verschiedene Dateiformate unterstützt, z. B. *.csv, damit Sie die Ergebnisse in externen Tools wie Tabellen erfassen können. Diese Methode kann nützlich sein, wenn Sie Ergebnisse einfach für Teams freigeben möchten, die die Workbench nicht installiert haben.|
|[IBM DB2 LUW Inventory Scripts and Artifacts (IBM DB2 LUW-Inventarskripts und -artefakte)](https://github.com/Microsoft/DataMigrationTeam/tree/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Diese Ressource umfasst eine SQL-Abfrage für die Systemtabellen von Version 11.1 von IBM DB2 LUW und bietet eine Zählung von Objekten nach Schema und Objekttyp, eine grobe Schätzung für „Rohdaten“ in jedem Schema und die Dimensionierung von Tabellen in jedem Schema, wobei die Ergebnisse im CSV-Format gespeichert werden.|
|[Leitfaden für die Einrichtung von DB2 LUW pureScale in Azure](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)|Dieser Leitfaden dient als Ausgangspunkt für einen DB2-Implementierungsplan. Auch wenn Geschäftsanforderungen unterschiedlich sind, gilt dennoch dasselbe grundlegende Muster. Dieses Architekturmuster kann auch für OLAP-Anwendungen in Azure verwendet werden.|

Diese Ressourcen wurden im Rahmen des Data SQL Ninja-Programms entwickelt, das vom Azure Data Group-Entwicklungsteam gesponsert wird. Der Hauptzweck des Data SQL Ninja-Programms besteht darin, Hindernisse für komplexe Modernisierung zu beseitigen und Letztere zu beschleunigen sowie den Wettbewerb in Bezug auf Möglichkeiten zur Migration von Datenplattformen zur Azure-Datenplattform von Microsoft zu fördern. Wenn Sie denken, dass Ihre Organisation an einer Teilnahme am Data SQL Ninja-Programm interessiert wäre, wenden Sie sich an Ihr Kundenteam, und bitten Sie es, eine Nominierung einzureichen.


## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die Dienste und Tools von Microsoft und Drittanbietern, mit denen Sie verschiedene Datenbank- und Datenmigrationsszenarios sowie Spezialaufgaben ausführen können, finden Sie unter [Dienste und Tools für die Datenmigration](../../../dms/dms-tools-matrix.md).

- Weitere Informationen zu Azure SQL Managed Instance finden Sie unter:
   - [Übersicht über SQL Managed Instance](../../managed-instance/sql-managed-instance-paas-overview.md)
   - [Azure-Gesamtkostenrechner](https://azure.microsoft.com/pricing/tco/calculator/) 


- Weitere Informationen zum Framework und zum Einführungszyklus von Cloudmigrationen finden Sie unter:
   -  [Cloud Adoption Framework für Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Bewährte Methoden für die Kostenermittlung und Größenanpassung von zu Azure migrierten Workloads](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Informationen zum Bewerten der Anwendungszugriffsebene finden Sie unter [Data Access Migration Toolkit (Vorschauversion)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Ausführliche Informationen zum Ausführen von A/B-Tests auf Datenzugriffsebene finden Sie unter [Assistent für Datenbankexperimente](/sql/dea/database-experimentation-assistant-overview).