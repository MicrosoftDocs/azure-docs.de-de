---
title: Migrieren von Azure-Analyseressourcen – Azure Deutschland zu Azure weltweit
description: Dieser Artikel enthält Informationen zum Migrieren von Azure-Analyseressourcen von Azure Deutschland zu Azure weltweit.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: d1bb03d1d4ad7a66a2726721de11c40875a78131
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018587"
---
# <a name="migrate-analytics-resources-to-global-azure"></a>Migrieren von Analyseressourcen zu Azure weltweit

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]


Dieser Artikel enthält Informationen dazu, wie Sie Azure-Analyseressourcen von Azure Deutschland zu Azure weltweit migrieren können.
  
## <a name="event-hubs"></a>Event Hubs

Es ist nicht möglich, Azure Event Hubs-Ressourcen direkt von Azure Deutschland zu Azure weltweit zu migrieren. Der Event Hubs-Dienst hat keine Datenexport- und -importfunktionen. Sie können Event Hubs-Ressourcen jedoch [als eine Vorlage](../azure-resource-manager/templates/export-template-portal.md) exportieren. Passen Sie dann die exportierte Vorlage für Azure weltweit an, und erstellen Sie die Ressourcen erneut.

> [!NOTE]
> Bei einem Exportieren einer Event Hubs-Vorlage werden keine Daten (z. B. Nachrichten) kopiert. Bei einem Exportieren einer Vorlage werden nur die Event Hubs-Metadaten neu erstellt.

> [!IMPORTANT]
> Ändern Sie den Standort, Azure Key Vault-Geheimnisse, Zertifikate und andere GUIDs, damit diese konsistent mit der neuen Region sind.

### <a name="event-hubs-metadata"></a>Event Hubs-Metadaten

Die folgenden Metadatenelemente werden neu erstellt werden, wenn Sie eine Event Hubs-Vorlage exportieren:

- Namespaces
- Event Hubs
- Verbrauchergruppen
- Autorisierungsregeln

Weitere Informationen finden Sie unter:

- Lesen Sie die [Übersicht über Event Hubs](../event-hubs/event-hubs-about.md).
- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Tutorials zu Event Hubs](../event-hubs/index.yml) durcharbeiten.
- Überprüfen Sie die Migrationsschritte für [Azure Service Bus](./germany-migration-integration.md#service-bus).
- Machen Sie sich damit vertraut, wie Sie [Azure Resource Manager-Vorlagen exportieren](../azure-resource-manager/templates/export-template-portal.md), oder lesen Sie die Übersicht zu [Azure Resource Manager](../azure-resource-manager/management/overview.md).

## <a name="hdinsight"></a>HDInsight

So migrieren Sie Azure HDInsight-Cluster von Azure Deutschland zu Azure weltweit:

1. Beenden Sie den HDInsight-Cluster.
2. Migrieren Sie die Daten im Azure Storage-Konto mit AzCopy oder einem ähnlichen Tool zur neuen Region.
3. Erstellen Sie neue Computeressourcen in Azure weltweit, und fügen Sie die migrierten Speicherressourcen dann als den primären zugeordneten Speicher an.

Für spezialisiertere Cluster mit langer Ausführungszeit (Kafka, Spark-Streaming, Storm oder HBase) empfiehlt es sich, dass Sie den Übergang von Workloads in die neue Region orchestrieren.

Weitere Informationen finden Sie unter:

- Lesen Sie die [Azure HDInsight-Dokumentation](../hdinsight/index.yml).
- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Tutorials zu HDInsight](../hdinsight/index.yml) durcharbeiten.
- Informationen zum [Skalieren von HDInsight-Clustern](../hdinsight/hdinsight-administer-use-powershell.md#scale-clusters) finden Sie unter [Verwalten von Apache Hadoop-Clustern in HDInsight mit Azure PowerShell](../hdinsight/hdinsight-administer-use-powershell.md).
- Erfahren Sie, wie [AzCopy](../storage/common/storage-use-azcopy-v10.md) verwendet wird.

## <a name="stream-analytics"></a>Stream Analytics

Um Azure Stream Analytics-Dienste von Azure Deutschland zu Azure weltweit zu migrieren, erstellen Sie das gesamte Setup in einer Azure weltweit-Region entweder über das Azure-Portal oder mit PowerShell manuell erneut. Eingangs- und Ausgangsquellen für einen Stream Analytics-Auftrag können sich in jeder beliebigen Region befinden.

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Tutorials zu Stream Analytics](../stream-analytics/stream-analytics-manage-job.md) durcharbeiten.
- Lesen Sie die [Übersicht über Stream Analytics](../stream-analytics/stream-analytics-introduction.md).
- Erfahren Sie, wie Sie [einen Stream Analytics-Auftrag über PowerShell erstellen](../stream-analytics/stream-analytics-quick-create-powershell.md).

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

## <a name="analysis-services"></a>Analysis Services

Um Ihre Azure Analysis Services-Modelle von Azure Deutschland zu Azure weltweit zu migrieren, verwenden Sie [Sicherungs- und Wiederherstellungsvorgänge](../analysis-services/analysis-services-backup.md).

Wenn Sie nur die Modellmetadaten und nicht die Daten migrieren möchten, ist das [erneute Bereitstellen des Modells über SQL Server Data Tools](../analysis-services/analysis-services-deploy.md) eine Alternative.

Weitere Informationen finden Sie unter:

- Erfahren Sie mehr über [Sichern und Wiederherstellen in Analysis Services](../analysis-services/analysis-services-backup.md).
- Lesen Sie die [Übersicht über Analysis Services](../analysis-services/analysis-services-overview.md).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Tools, Techniken und Empfehlungen zum Migrieren von Ressourcen in den folgenden Dienstkategorien:

- [Compute](./germany-migration-compute.md)
- [Netzwerk](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Datenbanken](./germany-migration-databases.md)
- [IoT](./germany-migration-iot.md)
- [Integration](./germany-migration-integration.md)
- [Identität](./germany-migration-identity.md)
- [Security](./germany-migration-security.md)
- [Verwaltungstools](./germany-migration-management-tools.md)
- [Medien](./germany-migration-media.md)