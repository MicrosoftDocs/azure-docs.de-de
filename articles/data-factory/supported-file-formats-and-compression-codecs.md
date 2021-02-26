---
title: Von der Copy-Aktivität unterstützte Dateiformate in Azure Data Factory
description: In diesem Thema werden die Dateiformate und Komprimierungscodecs beschrieben, die von der Copy-Aktivität in Azure Data Factory unterstützt werden.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jingwang
ms.openlocfilehash: bb773d01124b99b4837f393b610e00ecbfa510fb
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364612"
---
# <a name="supported-file-formats-and-compression-codecs-by-copy-activity-in-azure-data-factory"></a>Von der Copy-Aktivität unterstützte Dateiformate und Komprimierungscodecs in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*Dieser Artikel bezieht sich auf die folgenden Connectors: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Dateisystem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md) und [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Sie können die [Kopieraktivität](copy-activity-overview.md) verwenden, um Dateien unverändert zwischen zwei dateibasierten Datenspeichern zu kopieren. In diesem Fall werden die Daten effizient ohne Serialisierung oder Deserialisierung kopiert. 

Darüber hinaus können Sie auch Dateien in einem bestimmten Format analysieren oder generieren. Beispielsweise können Sie die Folgendes ausführen:

* Kopieren von Daten aus einer SQL Server-Datenbank und Schreiben in Azure Data Lake Storage Gen2 im Parquet-Format
* Kopieren von Dateien im Textformat (CSV) aus einem lokalen Dateisystem und Schreiben in Azure Blob Storage im Avro-Format
* Kopieren von ZIP-Dateien aus einem lokalen Dateisystem, direktes Dekomprimieren und Schreiben der extrahierten Dateien in Azure Data Lake Storage Gen2
* Kopieren von Daten im Gzip-komprimierten Textformat (CSV) aus Azure Blob Storage und Schreiben in Azure SQL-Datenbank
* Viele weitere Aktivitäten, die eine Serialisierung/Deserialisierung oder Komprimierung/Dekomprimierung erfordern

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den anderen Artikeln zur Kopieraktivität:

- [Kopieraktivität – Übersicht](copy-activity-overview.md)
- [Leistung der Kopieraktivität](copy-activity-performance.md)
