---
title: Migrieren von Daten aus Data Lake und Data Warehouse zu Azure
description: Verwenden von Azure Data Factory zum Migrieren von Daten aus einem Data Lake und Data Warehouse zu Azure.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/30/2019
ms.openlocfilehash: 4e7421cb8ea4b0c14e4f4b59a688cdb1afe7d462
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367706"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Verwenden von Azure Data Factory zum Migrieren von Daten aus einem Data Lake oder Data Warehouse zu Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Wenn Sie Ihren Data Lake oder Ihr Enterprise Data Warehouse (EDW) zu Microsoft Azure migrieren möchten, können Sie Azure Data Factory verwenden. Azure Data Factory eignet sich für folgende Szenarien:

- Migration von Big Data-Workloads aus Amazon Simple Storage Service (AWS S3) oder einem lokalen Hadoop Distributed File System (HDFS) zu Azure
- EDW-Migration aus Oracle Exadata, Netezza, Teradata oder Amazon Redshift zu Azure

Mit Azure Data Factory können bei der Data Lake-Migration Daten im Petabyte-Bereich (PB) und bei der Data Warehouse-Migration mehrere Terabytes (TB) an Daten verschoben werden.

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Warum Azure Data Factory für die Datenmigration verwendet werden kann

- Mit Azure Data Factory kann die Verarbeitungsleistung problemlos hochskaliert werden, um Daten serverlos mit hoher Leistung, Resilienz und Skalierbarkeit zu verschieben. Sie bezahlen dabei nur für das, was Sie tatsächlich nutzen. Beachten Sie dabei außerdem Folgendes: 
  - Datenmenge und Dateianzahl sind bei Azure Data Factory unbegrenzt.
  - Azure Data Factory kann Ihre Netzwerk- und Speicherbandbreite vollständig ausnutzen, um in Ihrer Umgebung den höchstmöglichen Datenverschiebungsdurchsatz zu erzielen.
  - Dank des nutzungsbasierten Zahlungsmodells von Azure Data Factory zahlen Sie nur für die Zeit, die tatsächlich für die Datenmigration zu Azure beansprucht wurde.  
- Azure Data Factory kann sowohl einen einmaligen historischen Ladevorgang als auch geplante inkrementelle Ladevorgänge durchführen.
- Azure Data Factory nutzt Azure Integration Runtime (IR), um Daten zwischen öffentlich zugänglichen Data Lake- und Data Warehouse-Endpunkten zu verschieben. Von Azure Data Factory kann auch eine selbstgehostete IR verwendet werden, um Daten für Data Lake- und Data Warehouse-Endpunkte innerhalb von Azure Virtual Network (VNET) oder hinter einer Firewall zu verschieben.
- Azure Data Factory bietet Sicherheit auf Unternehmensniveau: Sie können den Windows Installer (MSI) oder die Dienstidentität für eine sichere Dienst-zu-Dienst-Integration verwenden oder Anmeldeinformationen mithilfe von Azure Key Vault verwalten.
- Azure Data Factory bietet eine codefreie Erstellungsumgebung sowie ein umfangreiches integriertes Überwachungsdashboard.  

## <a name="online-vs-offline-data-migration"></a>Online- im Vergleich zu Offlinedatenmigration

Azure Data Factory ist ein Standardtool für die Onlinedatenmigration und ermöglicht die Übertragung von Daten über ein Netzwerk (Internet, ER oder VPN). Bei der Offlinemigration versenden Benutzer dagegen physische Datenübertragungsgeräte von ihrer Organisation an ein Azure-Rechenzentrum.  

Bei der Entscheidung zwischen Online- und Offlinemigration gibt es drei wichtige Kriterien:  

- Größe der zu migrierenden Daten
- Netzwerkbandbreite
- Migrationszeitfenster

Ein Beispiel: Angenommen, Sie möchten Ihre Datenmigration mithilfe von Azure Data Factory innerhalb von zwei Wochen (Ihr *Migrationszeitfenster*) abwickeln. Beachten Sie die rosafarbene/blaue Markierung in der folgenden Tabelle. Die unterste rosafarbene Zelle einer Spalte gibt jeweils die Kombination aus Datengröße/Netzwerkbandbreite an, deren Migrationszeitfenster den zwei Wochen am nächsten kommt, ohne es zu übersteigen. (Bei den blau eingefärbten Kombinationen aus Größe/Bandbreite wird das zweiwöchige Zeitfenster für die Onlinemigration jeweils überschritten.) 

![Online oder offline](media/data-migration-guidance-overview/online-offline.png) Anhand dieser Tabelle können Sie auf der Grundlage der Größe Ihrer Daten und Ihrer verfügbaren Netzwerkbandbreite ermitteln, ob Sie Ihr gewünschtes Migrationszeitfenster bei einer Onlinemigration (Azure Data Factory) einhalten können. Beträgt das Zeitfenster für die Onlinemigration mehr als zwei Wochen, empfiehlt sich eine Offlinemigration.

> [!NOTE]
> Bei der Onlinemigration können Sie mithilfe eines einzigen Tools im Rahmen eines End-to-End-Vorgangs sowohl historische Daten laden als auch inkrementelle Feeds nutzen.  Dadurch bleiben Ihre Daten während des gesamten Migrationszeitfensters zwischen dem vorhandenen Speicher und dem neuen Speicher synchronisiert. Somit können Sie Ihre ETL-Logik im neuen Speicher mit aktualisierten Daten neu erstellen.


## <a name="next-steps"></a>Nächste Schritte

- [Migrieren von Daten von AWS S3 zu Azure](data-migration-guidance-s3-azure-storage.md)
- [Migrieren der Daten von einem lokalen Hadoop-Cluster zu Azure](data-migration-guidance-hdfs-azure-storage.md)
- [Migrieren der Daten von einem lokalen Netezza-Server zu Azure](data-migration-guidance-netezza-azure-sqldw.md)
