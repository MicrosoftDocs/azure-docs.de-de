---
title: Selbsthilfe für serverlose SQL-Pools
description: Dieser Abschnitt enthält Informationen, die Sie bei der Behandlung von Problemen mit einem serverlosen SQL-Pool unterstützen.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 12ff369cb931eb36014b7c9598b036afdc158750
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96457184"
---
# <a name="self-help-for-serverless-sql-pool"></a>Selbsthilfe für serverlose SQL-Pools

In diesem Artikel erfahren Sie, wie Sie die häufigsten Probleme mit einem serverlosen SQL-Pool in Azure Synapse Analytics behandeln.

## <a name="serverless-sql-pool-is-grayed-out-in-synapse-studio"></a>Serverloser SQL-Pool (Vorschauversion) in Synapse Studio abgeblendet

Wenn von Synapse Studio keine Verbindung mit einem serverlosen SQL-Pool hergestellt werden kann, ist der serverlose SQL-Pool abgeblendet oder hat den Status „Offline“. Dieses Problem tritt in der Regel in einem der folgenden Fälle auf:

1) Ihr Netzwerk verhindert die Kommunikation mit dem Azure Synapse-Back-End. Dies ist meistens auf eine Blockierung des Ports 1443 zurückzuführen. Heben Sie die Blockierung dieses Ports auf, damit ein serverloser SQL-Pool verwendet werden kann. Es gibt auch noch andere Probleme, die dazu führen können, dass ein serverloser SQL-Pool nicht funktioniert. Weitere Informationen finden Sie im [vollständigen Leitfaden zur Problembehandlung](../troubleshoot/troubleshoot-synapse-studio.md).
2) Sie verfügen über keine Anmeldeberechtigungen für einen serverlosen SQL-Pool. Um Zugriff zu erhalten, muss Sie einer der Azure Synapse-Arbeitsbereichsadministratoren der Arbeitsbereichsadministrator- oder SQL-Administratorrolle hinzufügen. [Weitere Informationen finden Sie im vollständigen Leitfaden zur Zugriffssteuerung.](access-control.md)

## <a name="query-fails-because-file-cannot-be-opened"></a>Abfrage nicht erfolgreich, da Datei nicht geöffnet werden kann

Wenn bei Ihrer Abfrage ein Fehler mit dem Hinweis auftritt, dass die Datei nicht geöffnet werden kann, da sie nicht vorhanden ist oder von einem anderen Prozess verwendet wird, Sie aber sicher sind, dass die Datei vorhanden ist und nicht von einem anderen Prozess verwendet wird, kann vom serverlosen SQL-Pool nicht auf die Datei zugegriffen werden. Dieses Problem tritt in der Regel auf, wenn Ihre Azure Active Directory-Identität nicht über Zugriffsrechte für die Datei verfügt. Vom serverlosen SQL-Pool wird standardmäßig versucht, unter Verwendung Ihrer Azure Active Directory-Identität auf die Datei zuzugreifen. Um dieses Problem zu beheben, benötigen Sie die entsprechenden Zugriffsrechte für die Datei. Dies können Sie am einfachsten erreichen, indem Sie sich die Rolle „Mitwirkender an Storage-Blobdaten“ für das Speicherkonto zuweisen, für das Sie Abfragen ausführen möchten. [Weitere Informationen finden Sie im vollständigen Leitfaden zur Azure Active Directory-Zugriffssteuerung für Speicher.](../../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 

## <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>Abfrage nicht erfolgreich, da sie aufgrund von aktuellen Ressourceneinschränkungen nicht ausgeführt werden kann 

Wenn bei Ihrer Abfrage ein Fehler mit der Beschreibung auftritt, dass die Abfrage aufgrund von aktuellen Ressourceneinschränkungen nicht ausgeführt werden kann, gibt es in diesem Moment Ressourceneinschränkungen, die verhindern, dass der serverlose SQL-Pool die Abfrage ausführt: 

- Vergewissern Sie sich, dass Datentypen mit passener Größe verwendet werden. Geben Sie außerdem für Parquet-Dateien das Schema für Zeichenfolgenspalten an, da hierfür standardmäßig „VARCHAR(8000)“ verwendet wird. 

- Bei Abfragen für CSV-Dateien empfiehlt sich ggf. das [Erstellen von Statistiken](develop-tables-statistics.md#statistics-in-serverless-sql-pool). 

- Informationen zur Abfrageoptimierung finden Sie in den [bewährten Methoden zum Verbessern der Leistung von serverlosen SQL-Pools](best-practices-sql-on-demand.md).  

## <a name="create-statement-is-not-supported-in-master-database"></a>„CREATE STATEMENT“ wird in der Masterdatenbank nicht unterstützt

Wenn bei Ihrer Abfrage ein Fehler mit dem folgenden Hinweis auftritt:

> „Fehler beim Ausführen der Abfrage. Error: CREATE EXTERNAL TABLE/DATA SOURCE/DATABASE SCOPED CREDENTIAL/FILE FORMAT wird in der Masterdatenbank nicht unterstützt.“ 

bedeutet dies, dass die Masterdatenbank im serverlosen SQL-Pool die Erstellung von Folgendem nicht unterstützt:
  - Externe Tabellen
  - Externe Datenquellen
  - Datenbankweit gültige Anmeldeinformationen
  - Externe Dateiformate

Lösung:

  1. Erstellen Sie eine Benutzerdatenbank:

```sql
CREATE DATABASE <DATABASE_NAME>
```

  2. Führen Sie die CREATE-Anweisung im Kontext von <DATABASE_NAME> aus, die bei der Masterdatenbank zuvor fehlgeschlagen ist. 
  
  Beispiel für die Erstellung eines externen Dateiformats:
    
```sql
USE <DATABASE_NAME>
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] 
WITH ( FORMAT_TYPE = PARQUET)
```

## <a name="next-steps"></a>Nächste Schritte

Die folgenden Artikel enthalten weitere Informationen zur Verwendung eines serverlosen SQL-Pools:

- [Abfragen einer einzelnen CSV-Datei](query-single-csv-file.md)

- [Abfragen von Ordnern und mehreren CSV-Dateien](query-folders-multiple-csv-files.md)

- [Abfragen bestimmter Dateien](query-specific-files.md)

- [Abfragen von Parquet-Dateien](query-parquet-files.md)

- [Abfragen von geschachtelten Parquet-Typen](query-parquet-nested-types.md)

- [Abfragen von JSON-Dateien](query-json-files.md)

- [Erstellen und Verwenden von Sichten](create-use-views.md)

- [Erstellen und Verwenden externer Tabellen](create-use-external-tables.md)

- [Speichern von Abfrageergebnissen im Speicher](create-external-table-as-select.md)
