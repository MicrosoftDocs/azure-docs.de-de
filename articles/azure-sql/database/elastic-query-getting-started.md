---
title: Erstellen von Berichten für horizontal hochskalierte Clouddatenbanken
description: Verwenden Sie datenbankübergreifende Datenbankabfragen, um Berichte zu mehreren Datenbanken zu erstellen.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 10/10/2019
ms.openlocfilehash: 586dad7439cc57ed2c863ee5f6692e12f7a78c50
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92781225"
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Erstellen von Berichten für horizontal hochskalierte Clouddatenbanken
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Sie können mit einer [elastischen Abfrage](elastic-query-overview.md) Berichte von mehreren Datenbanken über einen einzigen Verbindungspunkt erstellen. Die Datenbanken müssen horizontal partitioniert werden (auch als „Sharding“ bezeichnet).

Wenn Sie über eine vorhandene Datenbank verfügen, finden Sie entsprechende Informationen unter [Migrieren von vorhandenen Datenbanken zu horizontaler Hochskalierung](elastic-convert-to-use-elastic-tools.md).

Informationen zu den für Abfragen benötigten SQL-Objekten finden Sie unter [Ausführen von Abfragen über horizontal partitionierte Clouddatenbanken (Sharddatenbanken) hinweg](elastic-query-horizontal-partitioning.md).

## <a name="prerequisites"></a>Voraussetzungen

Laden Sie das Beispiel [Erste Schritte mit den Tools für die elastische Datenbank](elastic-scale-get-started.md)herunter, und führen Sie es aus.

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Erstellen eines Shardzuordnungs-Managers mithilfe der Beispiel-App
Hier erstellen Sie einen Shardzuordnungs-Manager und mehrere Shards und fügen anschließend Daten in die Shards ein. Wenn Sie bereits über Shards verfügen, die Shardingdaten enthalten, können Sie die folgenden Schritte überspringen und zum nächsten Abschnitt wechseln.

1. Erstellen Sie die Beispielanwendung aus **Erste Schritte mit den Tools für die elastische Datenbank** , und führen Sie sie aus. Befolgen Sie dazu die Anweisungen im Artikelabschnitt [Herunterladen und Ausführen der Beispiel-App](elastic-scale-get-started.md#download-and-run-the-sample-app-1). Wenn Sie alle Schritte abgeschlossen haben, wird die folgende Eingabeaufforderung angezeigt:

    ![Eingabeaufforderung][1]
2. Geben Sie im Befehlsfenster „1“ ein, und drücken Sie die **EINGABETASTE** . Dadurch wird der Shardzuordnungs-Manager erstellt, und es werden zwei Shards zum Server hinzugefügt. Geben Sie dann „3“ ein, und drücken Sie die **EINGABETASTE** . Wiederholen Sie den Vorgang viermal. Dadurch werden Beispieldatenzeilen in die Shards eingefügt.
3. Im [Azure-Portal](https://portal.azure.com) sollten drei neue Datenbanken auf dem Server angezeigt werden:

   ![Visual Studio-Bestätigung][2]

   Zum diesem Zeitpunkt werden datenbankübergreifende Abfragen durch die Clientbibliotheken für elastische Datenbanken unterstützt. Verwenden Sie z. B. Option 4 im Eingabeaufforderungsfenster. Die Ergebnisse einer Abfrage mit mehreren Shards sind immer ein **UNION ALL** der Ergebnisse aus allen Shards.

   Im nächsten Abschnitt erstellen wir einen Endpunkt für eine Beispieldatenbank, der umfangreichere Abfragen der Daten über Shards hinweg unterstützt.

## <a name="create-an-elastic-query-database"></a>Erstellen einer elastischen Datenbankabfrage

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com), und melden Sie sich an.
2. Erstellen Sie eine neue Datenbank in Azure SQL-Datenbank auf demselben Server, auf dem sich auch das Shardsetup befindet. Nennen Sie die Datenbank "ElasticDBQuery".

    ![Azure-Portal und -Tarif][3]

    > [!NOTE]
    > Sie können eine vorhandene Datenbank verwenden. Wenn Sie diese Konfiguration wählen, darf es sich bei dem Shard nicht um einen der Shards handeln, für die Sie die Abfragen ausführen möchten. Diese Datenbank wird zum Erstellen der Metadatenobjekte für eine Abfrage für elastische Datenbanken verwendet.
    >

## <a name="create-database-objects"></a>Erstellen von Datenbankobjekten
### <a name="database-scoped-master-key-and-credentials"></a>Erstellen des Datenbankhauptschlüssels und der Anmeldeinformationen
Diese Informationen werden für die Verbindung mit dem Shard-Zuordnungs-Manager und den Shards verwendet:

1. Öffnen Sie SQL Server Management Studio oder SQL Server Data Tools in Visual Studio.
2. Stellen Sie eine Verbindung mit der ElasticDBQuery-Datenbank her, und führen Sie die folgenden T-SQL-Befehle aus:

    ```tsql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';

    CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
    WITH IDENTITY = '<username>',
    SECRET = '<password>';
    ```

    „username“ und „password“ müssen mit den Anmeldeinformationen aus Schritt 3 im Abschnitt [Herunterladen und Ausführen der Beispiel-App](elastic-scale-get-started.md#download-and-run-the-sample-app) im Artikel **Erste Schritte mit den Tools für elastische Datenbanken** übereinstimmen.

### <a name="external-data-sources"></a>Externe Datenquellen
Um eine externe Datenquelle zu erstellen, führen Sie den folgenden Befehl für die ElasticDBQuery-Datenbank aus:

```tsql
CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
    (TYPE = SHARD_MAP_MANAGER,
    LOCATION = '<server_name>.database.windows.net',
    DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
    CREDENTIAL = ElasticDBQueryCred,
    SHARD_MAP_NAME = 'CustomerIDShardMap'
) ;
```    

 "CustomerIDShardMap" ist der Name der Shard-Zuordnung, wenn Sie die Shard-Zuordnung und den Shard-Zuordnungs-Manager mithilfe des Beispiels für die Tools für elastische Datenbanken erstellt haben. Wenn Sie jedoch Ihre benutzerdefinierte Konfiguration für dieses Beispiel verwendet haben, sollte dies der Name Shard-Zuordnung sein, den Sie in der Anwendung ausgewählt haben.

### <a name="external-tables"></a>Externe Tabellen
Erstellen Sie eine externe Tabelle, die mit der Customers-Tabelle in den Shards übereinstimmt, indem Sie den folgenden Befehl für die ElasticDBQuery-Datenbank ausführen:

```tsql
CREATE EXTERNAL TABLE [dbo].[Customers]
( [CustomerId] [int] NOT NULL,
    [Name] [nvarchar](256) NOT NULL,
    [RegionId] [int] NOT NULL)
WITH
( DATA_SOURCE = MyElasticDBQueryDataSrc,
    DISTRIBUTION = SHARDED([CustomerId])
) ;
```

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Ausführen einer T-SQL-Abfrage für eine elastische Beispieldatenbank
Nachdem Sie die externe Datenquelle und die externen Tabellen definiert haben, können Sie jetzt vollständiges T-SQL in den externen Tabellen verwenden.

Führen Sie die folgende Abfrage für die ElasticDBQuery-Datenbank aus:

```tsql
select count(CustomerId) from [dbo].[Customers]
```

Sie werden feststellen, dass die Abfrage Ergebnisse aus allen Shards aggregiert und die folgende Ausgabe ausgibt:

![Ausgabedetails][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Importieren der Ergebnisse der Abfrage für die elastische Datenbank in Excel
 Sie können die Ergebnisse einer Abfrage in eine Excel-Datei importieren.

1. Öffnen Sie Excel 2013.
2. Navigieren Sie zum Menüband **Daten** .
3. Klicken Sie auf **Aus anderen Quellen** und dann auf **Von SQL Server** .

   ![Excel-Import aus anderen Quellen][5]
4. Geben Sie im **Datenverbindungs-Assistenten** den Servernamen und die Anmeldeinformationen ein. Klicken Sie dann auf **Weiter** .
5. Wählen Sie im Dialogfeld **Wählen Sie die Datenbank, die die benötigten Daten enthält** die Datenbank **ElasticDBQuery** aus.
6. Wählen Sie in der Listenansicht die Tabelle **Customers** aus, und klicken Sie auf **Weiter** . Klicken Sie auf **Fertig stellen** .
7. Wählen Sie im Formular **Daten importieren** unter **Wählen Sie das Format aus, in dem Sie diese Daten in der Arbeitsmappe anzeigen möchten** die Option **Tabelle** aus, und klicken Sie auf **OK** .

Die Excel-Tabelle wird mit allen Zeilen aus der Tabelle **Customers** gefüllt, die in verschiedenen Shards gespeichert sind.

Sie können jetzt die leistungsstarken Funktionen von Excel zur Datenvisualisierung verwenden. Sie können die Verbindungszeichenfolge mit dem Servernamen, Datenbanknamen und den Anmeldeinformationen zur Verbindung der BI und der Datenintegrationstools mit der elastischen Abfragedatenbank verwenden. Stellen Sie sicher, dass SQL Server als Datenquelle für das Tool unterstützt wird. Sie können auf die elastische Abfragedatenbank und die externen Tabellen wie auf jede andere SQL Server-Datenbank und SQL Server-Tabelle verweisen, zu denen Sie mit dem Tool eine Verbindung herstellen würden.

### <a name="cost"></a>Kosten
Es gibt keine zusätzlichen Gebühren für die Verwendung der Abfragefunktion für elastische Datenbanken.

Preisinformationen finden Sie in der [SQL-Datenbank – Preisdetails](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Nächste Schritte

* Eine Übersicht über elastische Abfragen finden Sie unter [Übersicht über elastische Abfragen in Azure SQL-Datenbank](elastic-query-overview.md).
* Ein Tutorial zur vertikalen Partitionierung finden Sie unter [Erste Schritte mit datenbankübergreifenden Abfragen (vertikale Partitionierung)](elastic-query-getting-started-vertical.md).
* Die Syntax und Beispiele für Abfragen von vertikal partitionierten Daten finden Sie unter [Abfragen von vertikal partitionierten Daten](elastic-query-vertical-partitioning.md).
* Die Syntax und Beispiele für Abfragen von horizontal partitionierten Daten finden Sie unter [Abfragen von horizontal partitionierten Daten](elastic-query-horizontal-partitioning.md).
* Unter [sp\_execute \_remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) finden Sie eine gespeicherte Prozedur, mit der eine Transact-SQL-Anweisung für eine einzelne Remoteinstanz von Azure SQL-Datenbank oder für eine Gruppe von Datenbanken ausgeführt wird, die als Shards in einem Schema mit horizontaler Partitionierung dienen.


<!--Image references-->
[1]: ./media/elastic-query-getting-started/cmd-prompt.png
[2]: ./media/elastic-query-getting-started/portal.png
[3]: ./media/elastic-query-getting-started/tiers.png
[4]: ./media/elastic-query-getting-started/details.png
[5]: ./media/elastic-query-getting-started/exel-sources.png
<!--anchors-->