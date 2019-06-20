---
title: Aktivität „Metadaten abrufen“ in Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die GetMetadata-Aktivität in einer Data Factory-Pipeline verwenden können.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: jingwang
ms.openlocfilehash: 78f63b4f46fe5479d4d0fd5849ad80536d8a137c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61346854"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Aktivität „Metadaten abrufen“ in Azure Data Factory

Die Aktivität „Metadaten abrufen“ kann zum Abrufen von **Metadaten** für alle Daten in Azure Data Factory verwendet werden. Diese Aktivität kann in folgenden Szenarien verwendet werden:

- Überprüfen der Metadateninformationen von Daten
- Auslösen einer Pipeline, wenn Daten bereit/verfügbar sind

Die folgende Funktionalität ist in der Ablaufsteuerung verfügbar:

- Die Ausgabe der Aktivität „Metadaten abrufen“ kann in bedingten Ausdrücken zur Validierung verwendet werden.
- Eine Pipeline kann über eine „Wiederholen bis“-Schleife ausgelöst werden, wenn die Bedingung erfüllt ist.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Die Aktivität „Metadaten abrufen“ nutzt ein Dataset als erforderliche Eingabe und gibt Metadateninformationen zurück, die als Ausgabe verfügbar sind. Derzeit werden die folgenden Connectors mit den entsprechenden abrufbaren Metadaten unterstützt, und die maximale unterstützte Metadatengröße beträgt **1 MB**.

>[!NOTE]
>Bei der Ausführung der Aktivität „Metadaten abrufen“ für eine selbst gehostete Integration Runtime wird die aktuelle Funktion unter Version 3.6 oder höher unterstützt. 

### <a name="supported-connectors"></a>Unterstützte Connectors

**Dateispeicher**:

| Connector/Metadaten | itemName<br>(Datei/Ordner) | itemType<br>(Datei/Ordner) | size<br>(Datei) | created<br>(Datei/Ordner) | lastModified<br>(Datei/Ordner) |childItems<br>(Ordner) |contentMD5<br>(Datei) | structure<br/>(Datei) | columnCount<br>(Datei) | exists<br>(Datei/Ordner) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Amazon S3 | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| Google Cloud Storage | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| Azure Blob | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| Azure Data Lake Storage Gen1 | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| Azure Data Lake Storage Gen2 | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| Azure File Storage | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| Dateisystem | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| SFTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| FTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |

- Für Amazon S3 und Google Sloud Storage gilt `lastModified` für Bucket und Schlüssel, aber nicht für den virtuellen Ordner. `exists` gilt für Bucket und Schlüssel, aber nicht für das Präfix oder den virtuellen Ordner.
- Für Azure Blob Storage gilt `lastModified` für Container und Blob, aber nicht für den virtuellen Ordner.

**Relationale Datenbank**:

| Connector/Metadaten | structure | columnCount | exists |
|:--- |:--- |:--- |:--- |
| Azure SQL-Datenbank | √ | √ | √ |
| Verwaltete Azure SQL-Datenbank-Instanz | √ | √ | √ |
| Azure SQL Data Warehouse | √ | √ | √ |
| SQL Server | √ | √ | √ |

### <a name="metadata-options"></a>Metadatenoptionen

Die folgenden Metadatentypen können im Feld zur Aktivität „Metadaten abrufen“ angegeben werden:

| Metadatentyp | BESCHREIBUNG |
|:--- |:--- |
| itemName | Name der Datei oder des Ordners. |
| itemType | Typ der Datei oder des Ordners. Der Ausgabewert lautet `File` oder `Folder`. |
| size | Größe der Datei in Byte. Gilt nur für Dateien. |
| created | Datum/Uhrzeit der Erstellung der Datei oder des Ordners. |
| lastModified | Datum/Uhrzeit der letzten Änderung der Datei oder des Ordners. |
| childItems | Liste der Unterordner und Dateien innerhalb des angegebenen Ordners. Gilt nur für Ordner. Der Ausgabewert ist eine Liste von Namen und Typen der einzelnen untergeordneten Elemente. |
| contentMD5 | MD5 der Datei. Gilt nur für Dateien. |
| structure | Datenstruktur innerhalb der Datei oder einer relationalen Datenbanktabelle. Der Ausgabewert ist eine Liste von Spaltennamen und Spaltentypen. |
| columnCount | Anzahl der Spalten in der Datei oder relationalen Tabelle. |
| exists| Gibt an, ob eine Datei, ein Ordner oder eine Tabelle vorhanden ist. Hinweis: Wenn „exists“ in der Feldliste „Metadaten abrufen“ angegeben ist, wird die Aktivität auch dann fehlerlos ausgeführt, wenn das Element (Datei/Ordner/Tabelle) nicht vorhanden ist. Stattdessen wird `exists: false` in der Ausgabe zurückgegeben. |

>[!TIP]
>Wenn Sie überprüfen möchten, ob eine Datei, ein Ordner oder eine Tabelle vorhanden ist, geben Sie in der Feldliste der Aktivität „Metadaten abrufen“ `exists` an, und überprüfen Sie dann das `exists: true/false`-Ergebnis der Aktivitätsausgabe. Ist `exists` nicht in der Feldliste konfiguriert, gibt die Aktivität „Metadaten abrufen“ einen Fehler aus, wenn das Objekt nicht gefunden wird.

## <a name="syntax"></a>Syntax

**Aktivität „Metadaten abrufen“** :

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```

**Dataset**:

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "filename": "file.json",
            "format":{
                "type":"JsonFormat"
            }
        }
    }
}
```

## <a name="type-properties"></a>Typeigenschaften

Die Aktivität „Metadaten abrufen“ kann zurzeit die folgenden Typen von Metadateninformationen abrufen.

Eigenschaft | BESCHREIBUNG | Erforderlich
-------- | ----------- | --------
fieldList | Listet die erforderlichen Typen der Metadateninformationen auf. Informationen zu unterstützten Metadaten finden Sie im Abschnitt [Metadatenoptionen](#metadata-options). | Ja 
dataset | Das Referenzdataset, dessen Metadatenaktivität von der Aktivität „Metadaten abrufen“ abgerufen werden soll. Die unterstützten Connectors werden im Abschnitt [Unterstützte Funktionen](#supported-capabilities) aufgeführt und im Thema zu Connectors finden Sie Einzelheiten zur Datasetsyntax. | Ja

## <a name="sample-output"></a>Beispielausgabe

Das Ergebnis „Metadaten abrufen“ wird in der Aktivitätsausgabe angezeigt. Im Folgenden werden zwei Beispiele mit umfangreichen Metadatenoptionen vorgestellt, die in der Feldliste als Verweis aktiviert sind. Um das Ergebnis in nachfolgenden Aktivitäten zu nutzen, verwenden Sie das Muster `@{activity('MyGetMetadataActivity').output.itemName}`.

### <a name="get-a-files-metadata"></a>Abrufen der Metadaten einer Datei

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>Abrufen der Metadaten eines Ordners

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter anderen Ablaufsteuerungsaktivitäten, die von Data Factory unterstützt werden: 

- [Aktivität „Pipeline ausführen“](control-flow-execute-pipeline-activity.md)
- [ForEach-Aktivität](control-flow-for-each-activity.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [Webaktivität](control-flow-web-activity.md)
