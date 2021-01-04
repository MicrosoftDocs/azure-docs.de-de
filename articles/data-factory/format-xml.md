---
title: XML-Format in Azure Data Factory
description: In diesem Thema wird der Umgang mit dem XML-Format in Azure Data Factory beschrieben.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: jingwang
ms.openlocfilehash: 1555d8b97f89b567cb1769c0ba10ecf50bca4366
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352596"
---
# <a name="xml-format-in-azure-data-factory"></a>XML-Format in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Verwenden Sie diesen Artikel, wenn Sie die **XML-Dateien analysieren** möchten. 

Das XML-Format wird für folgende Connectors unterstützt: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Dateisystem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md) und [SFTP](connector-sftp.md). Es wird als Quelle, aber nicht als Senke unterstützt.

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom XML-Dataset unterstützt werden.

| Eigenschaft         | BESCHREIBUNG                                                  | Erforderlich |
| ---------------- | ------------------------------------------------------------ | -------- |
| Typ             | Die type-Eigenschaft des Datasets muss auf **Xml** festgelegt werden. | Ja      |
| location         | Speicherorteinstellungen der Datei(en) Jeder dateibasierte Connector verfügt unter `location` über seinen eigenen Speicherorttyp und unterstützte Eigenschaften. **Informationen hierzu finden Sie im Abschnitt „Dataset-Eigenschaften“ des Artikels über Connectors**. | Ja      |
| encodingName     | Der zu Lesen/Schreiben von Testdateien verwendete Codierungstyp. <br>Es sind die folgenden Werte zulässig: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13", "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".| Nein       |
| nullValue | Gibt eine Zeichenfolgendarstellung von Null-Werten an.<br/>Der Standardwert ist eine **leere Zeichenfolge**. | Nein |
| compression | Gruppe von Eigenschaften zum Konfigurieren der Dateikomprimierung. Konfigurieren Sie diesen Abschnitt, wenn Sie während der Aktivitätsausführung eine Komprimierung/Dekomprimierung durchführen möchten. | Nein |
| type<br>(*unter `compression`* ) | Der zum Lesen und Schreiben von XML-Dateien verwendete Komprimierungscodec. <br>Zulässige Werte sind **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **TarGzip**, **Tar**, **snappy** und **lz4**. Standardmäßig erfolgt keine Komprimierung.<br>**Beachten Sie**, dass die Kopieraktivität zurzeit „snappy“ und „lz4“ und der Zuordnungsdatenfluss „ZipDeflate“, „TarGzip“ und „Tar“ nicht unterstützt.<br>**Beachten Sie** auch, dass bei Verwendung der Kopieraktivität zum Dekomprimieren von **ZipDeflate**-/**TarGzip**-/**Tar**-Dateien und zum Schreiben in den dateibasierten Senkendatenspeicher diese Dateien standardmäßig in den Ordner `<path specified in dataset>/<folder named as source compressed file>/` extrahiert werden. Verwenden Sie in diesem Fall `preserveZipFileNameAsFolder`/`preserveCompressionFileNameAsFolder` als [Quelle der Kopieraktivität](#xml-as-source), um zu steuern, ob der Name der komprimierten Dateien als Ordnerstruktur beibehalten werden soll. | Nein.  |
| level<br/>(*unter `compression`* ) | Das Komprimierungsverhältnis. <br>Zulässige Werte sind **Optimal** oder **Sehr schnell**.<br>- **Sehr schnell:** Der Komprimierungsvorgang wird schnellstmöglich abgeschlossen, auch wenn die resultierende Datei nicht optimal komprimiert ist.<br>- **Optimal**: Die Daten sollten optimal komprimiert sein, auch wenn der Vorgang eine längere Zeit in Anspruch nimmt. Weitere Informationen finden Sie im Thema [Komprimierungsstufe](/dotnet/api/system.io.compression.compressionlevel) . | Nein       |

Nachfolgend sehen Sie ein Beispiel für ein XML-Dataset in Azure Blob Storage:

```json
{
    "name": "XMLDataset",
    "properties": {
        "type": "Xml",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der XML-Quelle unterstützt werden.

Erfahren Sie, wie Sie XML-Daten und Senkendatenspeicher sowie das zugehörige Format aus der [Schemazuordnung](copy-activity-schema-and-type-mapping.md) zuordnen. Beim Anzeigen einer Vorschau der XML-Dateien werden Daten in der JSON-Hierarchie angezeigt, und Sie verweisen über den JSON-Pfad auf die Felder.

### <a name="xml-as-source"></a>XML als Quelle

Die folgenden Eigenschaften werden im Abschnitt **\_source\*** der Kopieraktivität unterstützt. Weitere Informationen finden Sie unter [Verhalten des XML-Connectors](#xml-connector-behavior).

| Eigenschaft      | Beschreibung                                                  | Erforderlich |
| ------------- | ------------------------------------------------------------ | -------- |
| Typ          | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf **XmlSource** festgelegt sein. | Ja      |
| formatSettings | Eine Gruppe von Eigenschaften. Weitere Informationen finden Sie in der Tabelle der **XML-Leseeinstellungen** weiter unten. | Nein       |
| storeSettings | Eine Gruppe von Eigenschaften für das Lesen von Daten aus einem Datenspeicher. Jeder dateibasierte Connector verfügt unter `storeSettings` über eigene unterstützte Leseeinstellungen. **Informationen hierzu finden Sie im Abschnitt über die Eigenschaften der Kopieraktivität im Artikel über Connectors**. | Nein       |

Unterstützte **XML-Leseeinstellungen** unter `formatSettings`:

| Eigenschaft      | Beschreibung                                                  | Erforderlich |
| ------------- | ------------------------------------------------------------ | -------- |
| Typ          | Der Typ von formatSettings muss auf **XmlReadSettings** festgelegt werden. | Ja      |
| validationMode | Gibt an, ob das XML-Schema validiert werden soll.<br>Zulässige Werte sind **none** (Standard, keine Validierung), **xsd** (Validierung mithilfe von XSD) und **dtd** (Validierung mithilfe von DTD). | Nein |
| Namespaces | Gibt an, ob beim Auswerten der XML-Dateien der Namespace aktiviert werden soll. Zulässige Werte sind **true** (Standard) oder **false** | Nein |
| namespacePrefixes | Namespace-URI für die Präfixzuordnung, die zum Benennen von Feldern beim Auswerten der XML-Datei verwendet wird.<br/>Wenn eine XML-Datei einen Namespace aufweist und der Namespace aktiviert ist, entspricht der Feldname standardmäßig dem im XML-Dokument.<br>Wenn ein Element für den Namespace-URI in dieser Zuordnung definiert ist, lautet der Feldname `prefix:fieldName`. | Nein |
| detectDataType | Gibt an, ob die Datentypen „integer“, „double“ und „boolesch“ erkannt werden sollen. Zulässige Werte sind **true** (Standard) oder **false**| Nein |
| compressionProperties | Eine Gruppe von Eigenschaften zur Festlegung, wie Daten bei einem bestimmten Komprimierungscodec dekomprimiert werden können. | Nein       |
| preserveZipFileNameAsFolder<br>(*unter `compressionProperties`->`type` als `ZipDeflateReadSettings`* )  | Diese Eigenschaft gilt, wenn das Eingabedataset mit der **ZipDeflate**-Komprimierung konfiguriert wurde. Sie gibt an, ob der Name der ZIP-Quelldatei während Kopiervorgängen als Ordnerstruktur beibehalten werden soll.<br>– Lautet der Wert **true** (Standard), schreibt Data Factory entzippte Dateien in `<path specified in dataset>/<folder named as source zip file>/`.<br>– Lautet der Wert **false**, schreibt Data Factory entzippte Dateien direkt in `<path specified in dataset>`. Stellen Sie sicher, dass keine doppelten Dateinamen in unterschiedlichen ZIP-Quelldateien vorliegen, um „Racing“ oder ein unerwartetes Verhalten zu vermeiden.  | Nein |
| preserveCompressionFileNameAsFolder<br>(*unter `compressionProperties`->`type` als `TarGZipReadSettings` oder `TarReadSettings`* ) | Gilt, wenn das Eingabedataset mit der **TarGzip**/**Tar**-Komprimierung konfiguriert wurde. Gibt an, ob der Name der komprimierten Quelldatei während Kopiervorgängen als Ordnerstruktur beibehalten werden soll.<br>– Lautet der Wert **true** (Standardwert), schreibt Data Factory dekomprimierte Dateien in `<path specified in dataset>/<folder named as source compressed file>/`. <br>– Lautet der Wert **false**, schreibt Data Factory dekomprimierte Dateien direkt in `<path specified in dataset>`. Stellen Sie sicher, dass keine doppelten Dateinamen in unterschiedlichen Quelldateien vorliegen, um Racebedingungen oder ein unerwartetes Verhalten zu vermeiden. | Nein |

## <a name="mapping-data-flow-properties"></a>Eigenschaften von Mapping Data Flow

Bei Zuordnungsdatenflüssen können Sie in den folgenden Datenspeichern das XML-Format lesen und schreiben: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) und [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties). Sie können auf XML-Dateien mit einem XML-Dataset oder einem [Inlinedataset](data-flow-source.md#inline-datasets) verweisen.

### <a name="source-properties"></a>Quelleigenschaften

In der folgenden Tabelle sind die von einer XML-Quelle unterstützten Eigenschaften aufgeführt. Sie können diese Eigenschaften auf der Registerkarte **Quelloptionen** bearbeiten. Weitere Informationen finden Sie unter [Verhalten des XML-Connectors](#xml-connector-behavior). Bei Verwendung eines Inlinedatasets werden zusätzliche Dateieinstellungen angezeigt. Diese entsprechen den Eigenschaften, die im Abschnitt zu den [Dataseteigenschaften](#dataset-properties) beschrieben sind. 

| Name | Beschreibung | Erforderlich | Zulässige Werte | Datenflussskript-Eigenschaft |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Platzhalterpfade | Alle Dateien, die dem Platzhalterpfad entsprechen, werden verarbeitet. Überschreibt den Ordner und den Dateipfad, die im Dataset festgelegt sind. | Nein | String[] | wildcardPaths |
| Partitionsstammpfad | Für partitionierte Dateidaten können Sie einen Partitionsstammpfad eingeben, um partitionierte Ordner als Spalten zu lesen. | Nein | String | partitionRootPath |
| Liste mit den Dateien | Gibt an, ob Ihre Quelle auf eine Textdatei verweist, in der die zu verarbeitenden Dateien aufgelistet sind. | Nein | `true` oder `false` | fileList |
| Spalte, in der der Dateiname gespeichert wird | Erstellt eine neue Spalte mit dem Namen und Pfad der Quelldatei. | Nein | String | rowUrlColumn |
| Nach Abschluss | Löscht oder verschiebt die Dateien nach der Verarbeitung. Dateipfad beginnt mit dem Containerstamm | Nein | Löschen: `true` oder `false` <br> Verschieben: `['<from>', '<to>']` | purgeFiles <br>moveFiles |
| Nach der letzten Änderung filtern | Filtern Sie Dateien nach dem Zeitpunkt ihrer letzten Änderung. | Nein | Timestamp | modifiedAfter <br>modifiedBefore |
| Validierungsmodus | Gibt an, ob das XML-Schema validiert werden soll. | Nein | `None` (Standard, keine Validierung)<br>`xsd` (Validierung mit XSD)<br>`dtd` (Validierung mit DTD) | validationMode |
| Namespaces | Gibt an, ob beim Auswerten der XML-Dateien der Namespace aktiviert werden soll. | Nein | `true` (Standard) oder `false` | Namespaces |
| Namespace-Präfix-Paare | Namespace-URI für die Präfixzuordnung, die zum Benennen von Feldern beim Auswerten der XML-Datei verwendet wird.<br/>Wenn eine XML-Datei einen Namespace aufweist und der Namespace aktiviert ist, entspricht der Feldname standardmäßig dem im XML-Dokument.<br>Wenn ein Element für den Namespace-URI in dieser Zuordnung definiert ist, lautet der Feldname `prefix:fieldName`. | Nein | Array mit Muster`['URI1'->'prefix1','URI2'->'prefix2']` | namespacePrefixes |
| Finden keiner Dateien zulässig | „true“ gibt an, dass kein Fehler ausgelöst wird, wenn keine Dateien gefunden werden. | nein | `true` oder `false` | ignoreNoFilesFound |

### <a name="xml-source-script-example"></a>Skriptbeispiel für XML-Quelle

Das nachstehende Skript stellt ein Beispiel für eine XML-Quellkonfiguration in Zuordnungsdatenflüssen per Datasetmodus dar.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

Das nachstehende Skript stellt ein Beispiel für eine XML-Quellkonfiguration mit dem Inline-Datasetmodus dar.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'xml',
    fileSystem: 'filesystem',
    folderPath: 'folder',
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

## <a name="xml-connector-behavior"></a>Verhalten des XML-Connectors

Beachten Sie Folgendes, wenn Sie XML als Quelle verwenden.

- XML-Attribute:

    - Attribute eines Elements werden als untergeordnete Felder des Elements in der Hierarchie analysiert.
    - Der Name des Attributfelds folgt dem Muster `@attributeName`.

- XML-Schemaüberprüfung:

    - Sie können entscheiden, das Schema nicht zu überprüfen oder die Überprüfung mithilfe von XSD oder DTD durchzuführen.
    - Wenn Sie XSD oder DTD zum Überprüfen von XML-Dateien verwenden, muss in den XML-Dateien über den relativen Pfad auf die XSD-/DTD-Datei verwiesen werden.

- Namespaceverarbeitung:

    - Der Namespace kann bei Verwendung von Datenflüssen deaktiviert werden. In diesem Fall werden die Attribute, die den Namespace definieren, als normale Attribute analysiert.
    - Wenn der Namespace aktiviert ist, folgen die Namen der Elemente und Attribute standardmäßig den Mustern `namespaceUri,elementName` und `namespaceUri,@attributeName`. Sie können ein Namespacepräfix für jeden Namespace-URI in der Quelle definieren. In diesem Fall folgen die Namen der Elemente und Attribute stattdessen den Mustern `definedPrefix:elementName` oder `definedPrefix:@attributeName`.

- Wertspalte:

    - Wenn ein XML-Element sowohl einen einfachen Textwert als auch Attribute und untergeordnete Elemente aufweist, wird der einfache Textwert als Wert einer „Wertspalte“ mit dem integrierten Feldnamen `_value_` analysiert. Außerdem erbt er ggf. den Namespace des Elements.

## <a name="next-steps"></a>Nächste Schritte

- [Kopieraktivität – Übersicht](copy-activity-overview.md)
- [Mapping Data Flow](concepts-data-flow-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [GetMetadata-Aktivität](control-flow-get-metadata-activity.md)