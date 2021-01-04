---
title: Migrieren von Azure Data Lake Storage von Gen1 zu Gen2
description: Migrieren Sie Azure Data Lake Storage von Gen1 zu Gen2, das auf Azure Blob Storage aufsetzt und eine Reihe von Funktionen für Big Data-Analysen bietet.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 03/11/2020
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: aeb2c58504d1f058a3b887e02a7b7406c09db5b6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913146"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>Migrieren von Azure Data Lake Storage von Gen1 zu Gen2

Sie können Ihre Daten, Workloads und Anwendungen von Data Lake Storage Gen1 zu Data Lake Storage Gen2 migrieren.

‎Azure Data Lake Storage Gen2 setzt auf [Azure Blob Storage](storage-blobs-introduction.md) auf und bietet eine Reihe von Funktionen für Big Data-Analysen. [Data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/) verbindet Features von [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml) wie Dateisystemsemantik, Sicherheit auf Verzeichnis- und Dateiebene und Skalierbarkeit mit den kostengünstigen, mehrstufigen Speicherlösungen sowie Hochverfügbarkeits- und Notfallwiederherstellungsfunktionen von [Azure Blob Storage](storage-blobs-introduction.md).

> [!NOTE]
> Zur besseren Lesbarkeit wird in diesem Artikel mit dem Begriff *Gen1* auf Azure Data Lake Storage Gen1 verwiesen und mit dem Begriff *Gen2* auf Azure Data Lake Storage Gen2.

## <a name="recommended-approach"></a>Empfohlene Vorgehensweise

Für die Migration zu Gen2 empfehlen wir die folgende Vorgehensweise.

:heavy_check_mark: Schritt 1: Bewerten der Bereitschaft

:heavy_check_mark: Schritt 2: Vorbereiten der Migration

:heavy_check_mark: Schritt 3: Migrieren von Daten und Anwendungsworkloads

:heavy_check_mark: Schritt 4: Umstellung von Gen1 auf Gen2

> [!NOTE]
> Gen1 und Gen2 sind verschiedene Dienste, es gibt keine direkte Upgrademethode, und die Migration muss ausdrücklich stattfinden. 

### <a name="step-1-assess-readiness"></a>Schritt 1: Bewerten der Bereitschaft

1. Erfahren Sie etwas über das [Data Lake Storage Gen2-Angebot](https://azure.microsoft.com/services/storage/data-lake-storage/) mit den verbundenen Vorteilen, Kosten und der allgemeinen Architektur. 

2. [Vergleichen Sie die Funktionen](#gen1-gen2-feature-comparison) von Gen1 mit denen von Gen2. 

3. Überprüfen Sie die Liste der [bekannten Probleme](data-lake-storage-known-issues.md), um etwaige Lücken in der Funktionalität zu bewerten.

4. Gen2 unterstützt Blob Storage-Features wie [Diagnoseprotokollierung](../common/storage-analytics-logging.md), [Zugriffsebenen](storage-blob-storage-tiers.md) und [Richtlinien für die Blob Storage-Lebenszyklusverwaltung](storage-lifecycle-management-concepts.md). Wenn Sie eines dieser Features verwenden möchten, erkundigen Sie sich über die [aktuelle Unterstützung](./data-lake-storage-supported-blob-storage-features.md).

5. Erkundigen Sie sich über den aktuellen Status der [Unterstützung im Azure-Ökosystem](./data-lake-storage-multi-protocol-access.md), um sicherzustellen, dass Gen2 die Dienste unterstützt, von denen Ihre Lösungen abhängig sind.

### <a name="step-2-prepare-to-migrate"></a>Schritt 2: Vorbereiten der Migration

1. Identifizieren Sie die Datasets, die Sie migrieren möchten.

   Nutzen Sie diese Gelegenheit, um Datasets zu bereinigen, die Sie nicht mehr verwenden. Wenn Sie nicht alle Daten gleichzeitig migrieren möchten, nutzen Sie die Zeit, um logische Datengruppen zu finden, die Sie in Phasen migrieren können.
   
2. Ermitteln Sie, welche Auswirkungen eine Migration auf Ihr Unternehmen hat.

   Bedenken Sie beispielsweise, ob Sie sich Ausfallzeiten während der Migration leisten können. Diese Überlegungen können Ihnen helfen, ein geeignetes Migrationsmuster zu ermitteln und die am besten geeigneten Tools auszuwählen.

3. Erstellen Sie einen Migrationsplan. 

   Wir empfehlen diese [Migrationsmuster](#migration-patterns). Sie können eines dieser Muster auswählen, sie miteinander kombinieren oder ein eigenes Muster entwerfen.

### <a name="step-3-migrate-data-workloads-and-applications"></a>Schritt 3: Migrieren von Daten, Workloads und Anwendungen

Migrieren Sie Daten, Workloads und Anwendungen mithilfe Ihres bevorzugten Musters. Wir empfehlen, Szenarien inkrementell zu validieren.

1. [Erstellen Sie ein Speicherkonto](../common/storage-account-create.md), und aktivieren Sie das Feature für hierarchische Namespaces. 

2. Migrieren Sie Ihre Daten. 

3. Konfigurieren Sie [Dienste in Ihren Workloads](./data-lake-storage-supported-azure-services.md), sodass sie auf Ihren Gen2-Endpunkt verweisen. 
   
4. Aktualisieren Sie Anwendungen für die Verwendung von Gen2-APIs. Weitere Informationen finden Sie in den Anleitungen für [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), [JavaScript](data-lake-storage-directory-file-acl-javascript.md) und [REST](/rest/api/storageservices/data-lake-storage-gen2). 
   
5. Aktualisieren Sie Skripts für die Verwendung der [PowerShell-Cmdlets](data-lake-storage-directory-file-acl-powershell.md) und [Azure CLI-Befehle](data-lake-storage-directory-file-acl-cli.md) für Data Lake Storage Gen2.
   
6. Suchen Sie nach URI-Verweisen, die die Zeichenfolge `adl://` in Codedateien oder in Databricks-Notebooks, Apache Hive-HQL-Dateien oder anderen Dateien in Ihren Workloads enthalten. Ersetzen Sie diese Verweise durch den [für Gen2 formatierten URI](data-lake-storage-introduction-abfs-uri.md) Ihres neuen Speicherkontos. Beispiel: Der Gen1-URI `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` könnte in `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile` geändert werden. 

7. Konfigurieren Sie die Sicherheit Ihres Kontos so, dass [Azure-Rollen](../common/storage-auth-aad-rbac-portal.md), [Sicherheit auf Datei- und Ordnerebene](data-lake-storage-access-control.md) und [Firewalls und virtuelle Netzwerke von Azure Storage](../common/storage-network-security.md) enthalten sind.

### <a name="step-4-cutover-from-gen1-to-gen2"></a>Schritt 4: Umstellung von Gen1 auf Gen2

Nachdem Sie sicher sind, dass Ihre Anwendungen und Workloads in Gen2 stabil ausgeführt werden, können Sie mit der Verwendung von Gen2 entsprechend Ihren geschäftlichen Szenarien beginnen. Deaktivieren Sie alle verbleibenden Pipelines, die mit Gen1 ausgeführt werden, und nehmen Sie Ihr Gen1-Konto außer Betrieb. 

<a id="gen1-gen2-feature-comparison"></a>

## <a name="gen1-vs-gen2-capabilities"></a>Funktionen von Gen1 im Vergleich zu Gen2

In dieser Tabelle werden die Funktionen von Gen1 mit denen von Gen2 verglichen.

|Bereich |Gen1   |Gen2 |
|---|---|---|
|Datenorganisation|[Hierarchischer Namespace](data-lake-storage-namespace.md)<br>Unterstützung von Dateien und Ordnern|[Hierarchischer Namespace](data-lake-storage-namespace.md)<br>Unterstützung von Containern, Dateien und Ordnern |
|Georedundanz| [LRS](../common/storage-redundancy.md#locally-redundant-storage)| [LRS](../common/storage-redundancy.md#locally-redundant-storage), [ZRS](../common/storage-redundancy.md#zone-redundant-storage), [GRS](../common/storage-redundancy.md#geo-redundant-storage), [RA-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|Authentifizierung|[Verwaltete AAD-Identität](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Dienstprinzipale](../../active-directory/develop/app-objects-and-service-principals.md)|[Verwaltete AAD-Identität](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Dienstprinzipale](../../active-directory/develop/app-objects-and-service-principals.md)<br>[Schlüssel für den gemeinsamen Zugriff](/rest/api/storageservices/authorize-with-shared-key)|
|Authorization|Verwaltung: [Azure RBAC](../../role-based-access-control/overview.md)<br>Daten: [ACLs](data-lake-storage-access-control.md)|Verwaltung: [Azure RBAC](../../role-based-access-control/overview.md)<br>Daten: [ACLs](data-lake-storage-access-control.md), [Azure RBAC](../../role-based-access-control/overview.md) |
|Verschlüsselung: ruhende Daten|Serverseitig: mit [von Microsoft verwalteten](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) oder [vom Kunden verwalteten](../common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) Schlüsseln|Serverseitig: mit [von Microsoft verwalteten](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) oder [vom Kunden verwalteten](../common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) Schlüsseln|
|VNET-Unterstützung|[VNET-Integration](../../data-lake-store/data-lake-store-network-security.md)|[Dienstendpunkte](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [private Endpunkte](../common/storage-private-endpoints.md)|
|Entwicklerumgebung|[REST](../../data-lake-store/data-lake-store-data-operations-rest-api.md), [.NET](../../data-lake-store/data-lake-store-data-operations-net-sdk.md), [Java](../../data-lake-store/data-lake-store-get-started-java-sdk.md), [Python](../../data-lake-store/data-lake-store-data-operations-python.md), [PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md), [Azure-Befehlszeilenschnittstelle](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|Allgemein verfügbar: [REST](/rest/api/storageservices/data-lake-storage-gen2), [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md)<br>Public Preview: [JavaScript](data-lake-storage-directory-file-acl-javascript.md), [PowerShell](data-lake-storage-directory-file-acl-powershell.md), [Azure-Befehlszeilenschnittstelle](data-lake-storage-directory-file-acl-cli.md)|
|Ressourcenprotokolle|Klassische Protokolle<br>[Azure Monitor (integriert)](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[Klassische Protokolle:](../common/storage-analytics-logging.md) allgemein verfügbar<br>Azure Monitor-Integration – zeitlicher Ablauf wird noch festgelegt|
|Ökosystem|[HDInsight (3.6)](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md), [Azure Databricks (ab 3.1)](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html), [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md), [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight (3.6, 4.0)](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md), [Azure Databricks (ab 5.1)](/azure/databricks/data/data-sources/azure/azure-datalake-gen2), [Azure Synapse Analytics](../../azure-sql/database/vnet-service-endpoint-rule-overview.md), [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns"></a>

## <a name="gen1-to-gen2-patterns"></a>Muster für Gen1 zu Gen2

Wählen Sie ein Migrationsmuster aus, und ändern Sie dieses Muster nach Bedarf.

|||
|---|---|
|**Lift & Shift**|Das einfachste Muster. Ideal, wenn Ausfallzeiten für Ihre Datenpipelines kein Problem darstellen.|
|**Inkrementelles Kopieren**|Ähnlich *Lift & Shift*, aber mit geringeren Ausfallzeiten. Ideal für große Datenmengen, bei denen das Kopieren länger dauert.|
|**Zwei Pipelines**|Ideal für Pipelines, bei denen keine Ausfallzeiten auftreten dürfen.|
|**Bidirektionale Synchronisierung**|Vergleichbar mit dem *Muster mit zwei Pipelines*, aber mit einer feiner abgestuften Herangehensweise, die für kompliziertere Pipelines geeignet ist.|

Diese Muster sollen nun genauer untersucht werden.
 
### <a name="lift-and-shift-pattern"></a>Lift & Shift-Muster

Dies ist das einfachste Muster. 

1. Beenden Sie alle Schreibvorgänge in Gen1.

2. Verschieben Sie Daten von Gen1 zu Gen2. Wir empfehlen [Azure Data Factory](../../data-factory/connector-azure-data-lake-storage.md). Die ACLs werden mit den Daten kopiert.

3. Leiten Sie Erfassungsvorgänge und Workloads zu Gen2 weiter.

4. Setzen Sie Gen1 außer Betrieb.

Sehen Sie sich den Beispielcode für das Lift & Shift-Muster im [Lift & Shift-Migrationsbeispiel](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Lift%20and%20Shift/README.md) an.

> [!div class="mx-imgBorder"]
> ![Lift & Shift-Muster](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>Überlegungen zur Verwendung des Lift & Shift-Musters

:heavy_check_mark: Wechseln Sie für alle Workloads gleichzeitig von Gen1 zu Gen2.

:heavy_check_mark: Gehen Sie während der Migration und des Umstellungszeitraums von Ausfallzeiten aus.

:heavy_check_mark: Ideal für Pipelines, bei denen Ausfallzeiten kein Problem darstellen und alle Apps gleichzeitig aktualisiert werden können.

### <a name="incremental-copy-pattern"></a>Muster mit inkrementellem Kopieren

1. Beginnen Sie damit, Daten von Gen1 zu Gen2 zu verschieben. Wir empfehlen [Azure Data Factory](../../data-factory/connector-azure-data-lake-storage.md). Die ACLs werden mit den Daten kopiert.

2. Kopieren Sie neue Daten inkrementell von Gen1.

3. Nachdem alle Daten kopiert wurden, beenden Sie alle Schreibvorgänge in Gen1, und leiten Sie Workloads an Gen2 weiter.

4. Setzen Sie Gen1 außer Betrieb.

Sehen Sie sich den Beispielcode für das Muster mit inkrementellem Kopieren in unserem [Beispiel für die Migration mit inkrementellem Kopieren](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Incremental/README.md) an.


> [!div class="mx-imgBorder"]
> ![Muster mit inkrementellem Kopieren](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>Überlegungen zur Verwendung des Musters mit inkrementellem Kopieren:

:heavy_check_mark: Wechseln Sie für alle Workloads gleichzeitig von Gen1 zu Gen2.

:heavy_check_mark: Sie müssen nur während der Umstellung von Ausfallzeiten ausgehen.

:heavy_check_mark: Ideal für Pipelines, bei denen alle Apps gleichzeitig aktualisiert werden, aber für die Datenkopie mehr Zeit erforderlich ist.

### <a name="dual-pipeline-pattern"></a>Muster mit zwei Pipelines

1. Verschieben Sie Daten von Gen1 zu Gen2. Wir empfehlen [Azure Data Factory](../../data-factory/connector-azure-data-lake-storage.md). Die ACLs werden mit den Daten kopiert.

2. Erfassen Sie neue Daten sowohl in Gen1 als auch in Gen2.

3. Leiten Sie Workloads an Gen2 weiter.

4. Beenden Sie alle Schreibvorgänge in Gen1, und setzen Sie Gen1 dann außer Betrieb.

Sehen Sie sich den Beispielcode für das Muster mit zwei Pipelines in unserem [Beispiel für die Migration mit zwei Pipelines](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Dual%20pipeline/README.md) an.

> [!div class="mx-imgBorder"]
> ![Muster mit zwei Pipelines](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>Überlegungen zur Verwendung des Musters mit zwei Pipelines:

:heavy_check_mark: Gen1- und Gen2-Pipelines werden parallel ausgeführt.

:heavy_check_mark: Unterstützt Umstellungen ohne Ausfallzeiten.

:heavy_check_mark: Ideal für Situationen, in denen bei Ihren Workloads und Anwendungen keine Ausfallzeiten auftreten dürfen und die Erfassung in beiden Speicherkonten erfolgen kann.

### <a name="bi-directional-sync-pattern"></a>Muster mit bidirektionaler Synchronisierung

1. Richten Sie die bidirektionale Replikation zwischen Gen1 und Gen2 ein. Wir empfehlen [WANDisco](https://docs.wandisco.com/bigdata/wdfusion/adls/). Dieses Tool bietet eine Reparaturfunktion für vorhandene Daten.

3. Wenn alle Verschiebungen abgeschlossen sind, beenden Sie alle Schreibvorgänge in Gen1, und deaktivieren die bidirektionale Replikation.

4. Setzen Sie Gen1 außer Betrieb.

Sehen Sie sich den Beispielcode für das Muster mit bidirektionaler Synchronisierung im [Beispiel für die Migration mit bidirektionaler Synchronisierung](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Bi-directional/README.md) an.

> [!div class="mx-imgBorder"]
> ![Muster mit bidirektionaler Synchronisierung](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>Überlegungen zur Verwendung des Musters mit bidirektionaler Synchronisierung:

:heavy_check_mark: Ideal für komplexe Szenarien mit einer großen Anzahl von Pipelines und Abhängigkeiten, in denen ein Verfahren in mehreren Stufen möglicherweise sinnvoller ist.  

:heavy_check_mark: Der Migrationsaufwand ist hoch, das Muster bietet aber eine gleichzeitige Unterstützung für Gen1 und Gen2.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die verschiedenen Aspekte der Einrichtung der Sicherheit für ein Speicherkonto. Weitere Informationen finden Sie im [Azure Storage-Sicherheitsleitfaden](./security-recommendations.md).
- Optimieren Sie die Leistung für Ihren Data Lake Store. Weitere Informationen finden Sie unter [Optimieren von Azure Data Lake Storage Gen2 im Hinblick auf Leistung](data-lake-storage-performance-tuning-guidance.md).
- Informieren Sie sich über die bewährten Methoden für die Verwaltung Ihres Data Lake Store. Weitere Informationen finden Sie unter [Bewährte Methoden zur Verwendung von Azure Data Lake Storage Gen2](data-lake-storage-best-practices.md).