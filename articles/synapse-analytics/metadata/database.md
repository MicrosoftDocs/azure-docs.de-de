---
title: Freigegebene Datenbank
description: Azure Synapse Analytics bietet ein Modell mit gemeinsam genutzten Metadaten, das es ermöglicht, über die zugehörige Engine für serverlose SQL-Pools und die SQL-Pool-Engine auf eine in einem serverlosen Apache Spark-Pool erstellte Datenbank zuzugreifen.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 14ae8b8670db53761d085bc019711e829633601b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451822"
---
# <a name="azure-synapse-analytics-shared-database"></a>Gemeinsam genutzte Azure Synapse Analytics-Datenbank

Azure Synapse Analytics ermöglicht den verschiedenen Berechnungsengines von Arbeitsbereichen die gemeinsame Nutzung von Datenbanken und Tabellen zwischen serverlosen Apache Spark-Pools und der Engine für serverlose SQL-Pools.

Eine mit einem Spark-Auftrag erstellte Datenbank wird mit dem gleichen Namen für alle aktuellen und zukünftigen Spark-Pools im Arbeitsbereich sichtbar (einschließlich der Engine für serverlose SQL-Pools).

Die Spark-Standarddatenbank `default` wird ebenfalls im Kontext des serverlosen SQL-Pools als Datenbank namens `default` angezeigt.

Da die Datenbanken asynchron mit dem serverlosen SQL-Pool synchronisiert werden, kommt es zu einer Verzögerung bei der Anzeige.

## <a name="manage-a-spark-created-database"></a>Verwalten einer von Spark erstellten Datenbank

Verwalten Sie von Spark erstellte Datenbanken mithilfe von Spark. Löschen Sie sie beispielsweise über einen Spark-Poolauftrag, und erstellen Sie Tabellen in der Datenbank über Spark.

Wenn Sie mithilfe eines serverlosen SQL-Pools Objekte in einer von Spark erstellten Datenbank erstellen oder versuchen, die Datenbank zu löschen, ist der Vorgang erfolgreich. Die ursprüngliche Spark-Datenbank wird jedoch nicht geändert.

## <a name="how-name-conflicts-are-handled"></a>Behandeln von Namenskonflikten

Im Falle eines Namenskonflikts zwischen einer Spark-Datenbank und einer vorhandenen Datenbank im serverlosen SQL-Pool wird im serverlosen SQL-Pool ein Suffix an die Spark-Datenbank angefügt. Das Suffix im serverlosen SQL-Pool lautet `_<workspace name>-ondemand-DefaultSparkConnector`.

Wenn also beispielsweise im Azure Synapse-Arbeitsbereich `myws` eine Spark-Datenbank mit dem Namen `mydb` erstellt wird und bereits eine Datenbank im serverlosen SQL-Pool mit dem gleichen Namen vorhanden ist, muss auf die Spark-Datenbank im serverlosen SQL-Pool mithilfe des Namens `mydb_myws-ondemand-DefaultSparkConnector` verwiesen werden.

> [!CAUTION]
> Vorsicht: Dieses Verhalten kann sich ggf. ändern. Verlassen Sie sich daher nicht darauf.

## <a name="security-model"></a>Sicherheitsmodell

Die Spark-Datenbanken und -Tabellen sowie deren synchronisierte Darstellungen in der SQL-Engine werden auf der zugrunde liegenden Speicherebene geschützt.

Der Sicherheitsprinzipal, von dem eine Datenbank erstellt wird, gilt als Besitzer der Datenbank und verfügt über alle Rechte für die Datenbank und die zugehörigen Objekte.

Wenn Sie einem Sicherheitsprinzipal (beispielsweise einem Benutzer oder einer Sicherheitsgruppe) Zugriff auf eine Datenbank gewähren möchten, stellen Sie die entsprechenden POSIX-Ordner- und -Dateiberechtigungen für die zugrunde liegenden Ordner und Dateien im Verzeichnis `warehouse` bereit. 

Damit also beispielsweise ein Sicherheitsprinzipal eine Tabelle in einer Datenbank lesen kann, müssen ausgehend vom Datenbankordner im Verzeichnis `warehouse` allen Ordnern Berechtigungen vom Typ `X` und `R` für diesen Sicherheitsprinzipal zugewiesen sein. Für Dateien (etwa die zugrunde liegenden Datendateien der Tabelle) sind außerdem Berechtigungen vom Typ `R` erforderlich. 

Wenn ein Sicherheitsprinzipal Objekte in einer Datenbank erstellen oder löschen können muss, sind zusätzliche Berechtigungen vom Typ `W` für die Ordner und Dateien im Ordner `warehouse` erforderlich.

## <a name="examples"></a>Beispiele

### <a name="create-and-connect-to-spark-database-with-serverless-sql-pool"></a>Erstellen einer Spark-Datenbank mit einem serverlosen SQL-Pool und Herstellen einer Verbindung mit dieser Datenbank

Erstellen Sie zunächst unter Verwendung eines Spark-Clusters, den Sie bereits in Ihrem Arbeitsbereich erstellt haben, eine neue Spark-Datenbank mit dem Namen `mytestdb`. Hierzu können Sie beispielsweise ein Spark-C#-Notebook mit der folgenden Anweisung vom Typ „.NET für Spark“ verwenden:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Nach einer kurzen Verzögerung wird die Datenbank aus dem serverlosen SQL-Pool angezeigt. Führen Sie beispielsweise die folgende Anweisung über den serverlosen SQL-Pool aus:

```sql
SELECT * FROM sys.databases;
```

Vergewissern Sie sich, dass `mytestdb` in den Ergebnissen enthalten ist.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu gemeinsam genutzten Metadaten von Azure Synapse Analytics finden Sie [hier](overview.md).
- Weitere Informationen zu gemeinsam genutzten Metadatentabellen von Azure Synapse Analytics finden Sie [hier](table.md).
