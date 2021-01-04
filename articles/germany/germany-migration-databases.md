---
title: Migrieren von Azure-Datenbankressourcen – Azure Deutschland zu Azure weltweit
description: Dieser Artikel enthält Informationen zum Migrieren von Azure-Datenbankressourcen von Azure Deutschland zu Azure weltweit.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: 2692b07deb8e13222214a363f2c99c1b5e9a5aef
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "96012434"
---
# <a name="migrate-database-resources-to-global-azure"></a>Migrieren von Datenbankressourcen zu Azure weltweit

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Dieser Artikel enthält Informationen dazu, wie Sie Azure-Datenbankressourcen von Azure Deutschland zu Azure weltweit migrieren können.

## <a name="sql-database"></a>SQL-Datenbank

Um kleinere Azure SQL-Datenbank-Workloads zu migrieren, verwenden Sie die Exportfunktion, um eine BACPAC-Datei zu erstellen. Eine BACPAC-Datei ist eine komprimierte Datei (ZIP-Datei), die Metadaten und die Daten aus der SQL Server-Datenbank enthält. Nachdem Sie die BACPAC-Datei erstellt haben, können Sie die Datei in die Zielumgebung kopieren (z.B. mit AzCopy) und die Importfunktion verwenden, um die Datenbank erneut zu erstellen. Beachten Sie die folgenden Aspekte:

- Damit ein Export im Hinblick auf Transaktionen konsistent ist, müssen Sie sicherstellen, dass eine der folgenden Bedingungen zutrifft:
  - Während des Exports tritt keine Schreibaktivität auf.
  - Sie exportieren aus einer bezüglich Transaktionen konsistenten Kopie Ihrer Azure SQL-Datenbank.
- Für einen Export in Azure-Blobspeicher ist die Größe der BACPAC-Datei auf 200 GB beschränkt. Führen Sie für größere BACPAC-Datei einen Export in lokalen Speicher aus.
- Falls der Exportvorgang aus der SQL-Datenbank länger als 20 Stunden dauert, wird der Vorgang möglicherweise abgebrochen. Suchen Sie in den folgenden Artikeln nach Tipps, wie die Leistung gesteigert werden kann.

> [!NOTE]
> Die Verbindungszeichenfolge ist nach dem Exportvorgang geändert, da der DNS-Name des Servers während des Exports geändert wird.

Weitere Informationen finden Sie unter:

- Erfahren Sie, wie Sie [eine Datenbank in eine BACPAC-Datei exportieren](../azure-sql/database/database-export.md).
- Erfahren Sie, wie Sie [eine BACPAC-Datei in eine neue Datenbank importieren](../azure-sql/database/database-import.md).
- Lesen Sie die [Dokumentation zu Azure SQL-Datenbank](/azure/sql-database/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

Wenn Sie Azure Synapse Analytics-Ressourcen von Azure Deutschland zu Azure weltweit migrieren möchten, führen Sie die unter „Azure SQL-Datenbank“ beschriebenen Schritte aus.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Sie können das Azure Cosmos DB-Datenmigrationstool verwenden, um Daten zu Azure Cosmos DB zu migrieren. Das Azure Cosmos DB-Datenmigrationstool ist eine Open-Source-Lösung, mit der Daten aus verschiedenen Quellen in Azure Cosmos DB importiert werden können. Folgende Quellen können verwendet werden: JSON-Dateien, MongoDB, SQL Server, CSV-Dateien, Azure-Tabellenspeicher, Amazon DynamoDB, HBase und Azure Cosmos-Container.


Das Azure Cosmos DB-Datenmigrationstool steht als GUI-Tool oder als Befehlszeilentool zur Verfügung. Der Quellcode ist im GitHub-Repository [Azure Cosmos DB Data Migration Tool](https://github.com/azure/azure-documentdb-datamigrationtool) verfügbar. Eine [kompilierte Version des Tools](https://www.microsoft.com/download/details.aspx?id=46436) ist im Microsoft Download Center verfügbar.

Um Azure Cosmos DB-Ressourcen zu migrieren, sollten Sie die folgenden Schritte ausführen:

1. Überprüfen Sie die Anforderungen an die Anwendungsbetriebszeit und die Kontokonfigurationen, um den besten Aktionsplan zu bestimmen.
1. Klonen Sie die Kontokonfigurationen aus Azure Deutschland in die neue Region, indem Sie das Datenmigrationstool ausführen.
1. Kann ein Wartungsfenster verwendet werden, kopieren Sie die Daten aus der Quelle in das Ziel, indem Sie das Datenmigrationstool ausführen.
1. Ist es nicht möglich, ein Wartungsfenster zu verwenden, kopieren Sie die Daten aus der Quelle in das Ziel, indem Sie das Tool und dann die folgenden Schritte ausführen:
   1. Verwenden Sie einen konfigurationsgesteuerten Ansatz, um Änderungen an den Lese-/Schreibvorgängen in einer Anwendung vorzunehmen.
   1. Führen Sie eine erstmalige Synchronisierung aus.
   1. Richten Sie eine inkrementelle Synchronisierung ein, und binden Sie den Änderungsfeed ein.
   1. Verknüpfen Sie Lesevorgänge mit dem neuen Konto, und überprüfen Sie die Anwendung.
   1. Beenden Sie Schreibvorgänge in das alte Konto, überprüfen Sie, ob der Änderungsfeed berücksichtigt wird, und verknüpfen Sie dann Schreibvorgänge mit dem neuen Konto.
   1. Beenden Sie das Tool, und löschen Sie das alte Konto.
1. Führen Sie das Tool aus, um zu überprüfen, ob die Daten im alten und im neuen Konto konsistent sind.

Weitere Informationen finden Sie unter:

- Informationen zur Verwendung des Datenmigrationstools finden Sie im [Tutorial: Migrieren Ihrer Daten zu Azure Cosmos DB mithilfe des Datenmigrationstools](../cosmos-db/import-data.md).
- Informationen zu Cosmos DB finden Sie unter [Willkommen bei Azure Cosmos DB](../cosmos-db/introduction.md).


## <a name="azure-cache-for-redis"></a>Azure Cache for Redis

Sie haben einige Optionen, wenn Sie eine Azure Cache für Redis-Instanz von Azure Deutschland zu Azure weltweit migrieren möchten. Welche Option Sie auswählen, hängt von Ihren Anforderungen ab.

### <a name="option-1-accept-data-loss-create-a-new-instance"></a>Option 1: Akzeptieren von Datenverlust, Erstellen einer neuen Instanz

Dieser Ansatz ist am sinnvollsten, wenn die beiden folgenden Bedingungen zutreffen:

- Sie verwenden Azure Cache for Redis als transienten Datencache.
- Ihre Anwendung füllt die Cachedaten in der neuen Region automatisch erneut auf.

So migrieren Sie mit Datenverlust und erstellen Sie eine neue Instanz:

1. Erstellen Sie eine neue Azure Cache for Redis-Instanz in der neuen Zielregion.
1. Ändern Sie Ihre Anwendung so, dass sie die neue Instanz in der neuen Region verwendet.
1. Löschen Sie die alte Azure-Cache für Redis-Instanz in der Quellregion.

### <a name="option-2-copy-data-from-the-source-instance-to-the-target-instance"></a>Option 2: Kopieren von Daten aus der Quellinstanz in die Zielinstanz

Ein Mitglied des Azure Cache for Redis-Teams hat ein Open-Source-Tool geschrieben, das Daten aus einer Azure Cache for Redis-Instanz in eine andere kopiert, ohne Import- oder Exportfunktionalität zu erfordern. Informationen zu dem Tool finden Sie in Schritt 4 in den folgenden Schritten.

So kopieren Sie Daten aus der Quellinstanz in die Zielinstanz:

1. Erstellen Sie einen virtuellen Computer in der Quellregion. Wenn Ihr Dataset in Azure Cache for Redis groß ist, stellen Sie sicher, dass Sie eine relativ leistungsfähige VM-Größe auswählen, um die für den Kopiervorgang benötigte Zeit zu minimieren.
1. Erstellen Sie eine neue Azure Cache for Redis-Instanz in der Zielregion.
1. Leeren Sie die Daten aus der **Ziel**-Instanz. (Achten Sie darauf, *nicht* die Daten aus der **Quell**-Instanz zu leeren. Das Leeren ist erforderlich, da das Kopiertool vorhandene Schlüssel am Zielspeicherort *nicht überschreibt*.)
1. Verwenden Sie das folgende Tool, um Daten automatisch aus der Azure Cache for Redis-Quellinstanz in die Azure Cache for Redis-Zielinstanz zu kopieren: [Quelle des Tools](https://github.com/deepakverma/redis-copy) und [Tool herunterladen](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip).

> [!NOTE]
> Dieser Vorgang kann abhängig von der Größe des Datasets sehr lange dauern.

### <a name="option-3-export-from-the-source-instance-import-to-the-destination-instance"></a>Option 3: Exportieren aus der Quellinstanz, Importieren in die Zielinstanz

Bei diesem Ansatz werden die Vorteile von Features genutzt, die nur im Premium-Tarif verfügbar sind.

So exportieren Sie aus der Quellinstanz und importieren Sie in die Zielinstanz:

1. Erstellen Sie im Premium-Tarif eine neue Azure Cache for Redis-Instanz in der Zielregion. Verwenden Sie dieselbe Größe wie für die Azure Cache for Redis-Quellinstanz.
1. [Exportieren Sie Daten aus dem Quellcache](../azure-cache-for-redis/cache-how-to-import-export-data.md), oder verwenden Sie das [PowerShell-Cmdlet „Export-AzRedisCache“](/powershell/module/az.rediscache/export-azrediscache).

   > [!NOTE]
   > Das Azure Storage-Exportkonto muss sich in derselben Region wie die Cacheinstanz befinden.

1. Kopieren Sie die exportierten Blobs in ein Speicherkonto in der Zielregion (z. B. mit AzCopy).
1. [Importieren Sie Daten in den Zielcache](../azure-cache-for-redis/cache-how-to-import-export-data.md), oder verwenden Sie das [PowerShell-Cmdlet „Import-AzRedisCache“](/powershell/module/az.rediscache/import-azrediscache).
1. Konfigurieren Sie Ihre Anwendung so, dass sie die Azure Cache for Redis-Zielinstanz verwendet.

### <a name="option-4-write-data-to-two-azure-cache-for-redis-instances-read-from-one-instance"></a>Option 4: Schreiben von Daten in zwei Azure Cache for Redis-Instanzen, Lesen aus einer Instanz

Bei diesem Ansatz müssen Sie Ihre Anwendung ändern. Die Anwendung muss Daten in mehrere Cacheinstanzen schreiben, während sie aus einer der Cacheinstanzen liest. Dieser Ansatz ist sinnvoll, wenn die in Azure-Cache für Redis gespeicherten Daten die folgenden Kriterien erfüllen:
- Die Daten werden in regelmäßigen Abständen aktualisiert. 
- Alle Daten werden in die Azure Cache for Redis-Zielinstanz geschrieben.
- Es ist genügend Zeit für die Aktualisierung aller Daten verfügbar.

Weitere Informationen finden Sie unter:

- Lesen Sie die [Übersicht über Azure-Cache für Redis](../azure-cache-for-redis/cache-overview.md).

## <a name="postgresql-and-mysql"></a>PostgreSQL und MySQL

Weitere Informationen finden Sie in den Artikeln im Abschnitt „Sichern und Migrieren von Daten“ in [PostgreSQL](../postgresql/index.yml) und [MySQL](../mysql/index.yml).

![PostgreSQL und MySQL](./media/germany-migration-main/databases.png)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Tools, Techniken und Empfehlungen zum Migrieren von Ressourcen in den folgenden Dienstkategorien:

- [Compute](./germany-migration-compute.md)
- [Netzwerk](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Integration](./germany-migration-integration.md)
- [Identität](./germany-migration-identity.md)
- [Security](./germany-migration-security.md)
- [Verwaltungstools](./germany-migration-management-tools.md)
- [Medien](./germany-migration-media.md)