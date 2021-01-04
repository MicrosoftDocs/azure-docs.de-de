---
title: Kopieren von Daten in und aus Azure Blob Storage
description: 'Hier erfahren Sie, wie Sie Blobdaten in Azure Data Factory kopieren. Verwenden Sie unser Beispiel: Kopieren von Daten aus Azure Blob Storage in Azure SQL-Datenbank (und umgekehrt).'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fa6e19fd9759d6e489d0945b5521a2e0ae3881e0
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462635"
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Kopieren von Daten nach oder aus Azure Blob Storage mithilfe von Azure Data Factory
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](data-factory-azure-blob-connector.md)
> * [Version 2 (aktuelle Version)](../connector-azure-blob-storage.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [Azure Blob Storage-Connector in V2](../connector-azure-blob-storage.md).


In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten nach und aus Azure Blob Storage zu kopieren. Dieser Artikel baut auf dem Artikel zu [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit der Kopieraktivität bietet.

## <a name="overview"></a>Übersicht
Sie können Daten aus einem beliebigen unterstützten Quelldatenspeicher in Azure Blob Storage bzw. aus Azure Blob Storage in einen beliebigen unterstützten Senkendatenspeicher kopieren. Die folgende Tabelle enthält eine Liste der Datenspeicher, die als Quellen oder Senken (Ziele) für die Kopieraktivität unterstützt werden. Beispielsweise können Sie Daten **aus** einer SQL Server-Datenbank oder einer Datenbank in Azure SQL-Datenbank **in** den Azure Blob Storage verschieben. Außerdem können Sie Daten **aus** Azure Blob Storage **in** Azure Synapse Analytics oder in eine Azure Cosmos DB-Sammlung kopieren.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Unterstützte Szenarios
Sie können Daten **aus Azure Blob Storage** in die folgenden Datenspeicher kopieren:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Sie können Daten aus den folgenden Datenspeichern **nach Azure Blob Storage** kopieren:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!IMPORTANT]
> Die Kopieraktivität unterstützt das Kopieren von Daten in und aus Azure Storage-Konten für allgemeine Zwecke und Blob Storage (Hot/Cool). Die Aktivität unterstützt das **Lesen aus Block-, Anfüge- und Seitenblobs**. Das **Schreiben wird jedoch nur für Blockblobs** unterstützt. Azure Premium Storage wird als Senke nicht unterstützt, da er vor dem Hintergrund von Seitenblobs funktioniert.
>
> Bei der Kopieraktivität werden die Daten nicht in der Quelle gelöscht, nachdem sie erfolgreich in das Ziel kopiert wurden. Wenn Sie Quelldaten nach dem erfolgreichen Kopieren löschen müssen, erstellen Sie eine [benutzerdefinierte Aktivität](data-factory-use-custom-activities.md), um die Daten zu löschen, und verwenden Sie die Aktivität in der Pipeline. Ein Beispiel finden Sie unter [Delete blob or folder](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity) (Löschen eines Blobs oder Ordners) auf GitHub.

## <a name="get-started"></a>Erste Schritte
Sie können eine Pipeline mit einer Kopieraktivität erstellen, die Daten mithilfe verschiedener Tools/APIs in und aus Azure Blob Storage verschiebt.

Am einfachsten erstellen Sie eine Pipeline mit dem **Kopier-Assistenten**. Dieser Artikel enthält eine [exemplarische Vorgehensweise](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) zur Erstellung einer Pipeline zum Kopieren von Daten von einem Azure Blob Storage-Standort an einen anderen Azure Blob Storage-Standort. Ein Tutorial zur Erstellung einer Pipeline zum Kopieren von Daten aus einer Azure Blob Storage-Instanz in Azure SQL-Datenbank finden Sie unter [Tutorial: Erstellen einer Pipeline mit dem Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md).

Sie können auch die folgenden Tools zum Erstellen einer Pipeline verwenden: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-Vorlage**, **.NET-API** und **REST-API**. Im [Tutorial zur Kopieraktivität](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) finden Sie detaillierte Anweisungen, wie Sie eine Pipeline mit einer Kopieraktivität erstellen können.

Unabhängig davon, ob Sie Tools oder APIs verwenden, führen Sie die folgenden Schritte aus, um eine Pipeline zu erstellen, die Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher verschiebt:

1. Eine **Data Factory**. Eine Data Factory kann eine oder mehrere Pipelines enthalten.
2. Erstellen **verknüpfter Dienste** zum Verknüpfen von Eingabe- und Ausgabedatenspeichern mit Ihrer Data Factory. Wenn Sie beispielsweise Daten aus Azure Blob Storage in Azure SQL-Datenbank kopieren, erstellen Sie zwei verknüpfte Dienste, um Ihr Azure Storage-Konto und Azure SQL-Datenbank mit Ihrer Data Factory zu verknüpfen. Informationen zu Eigenschaften von verknüpften Diensten, die spezifisch für Azure Blob Storage sind, finden Sie im Abschnitt [Eigenschaften des verknüpften Diensts](#linked-service-properties).
2. Erstellen von **Datasets** zur Darstellung von Eingabe- und Ausgabedaten für den Kopiervorgang. Im Beispiel, das im letzten Schritt erwähnt wurde, erstellen Sie ein Dataset, um den Blobcontainer und den Ordner mit den Eingabedaten anzugeben. Außerdem erstellen Sie ein weiteres Dataset zum Angeben der SQL-Tabelle in Azure SQL-Datenbank, in der die aus dem Blobspeicher kopierten Daten enthalten sind. Informationen zu Dataset-Eigenschaften, die spezifisch für Azure Blob Storage sind, finden Sie im Abschnitt [Dataset-Eigenschaften](#dataset-properties).
3. Erstellen einer **Pipeline** mit einer Kopieraktivität, die ein Dataset als Eingabe und ein Dataset als Ausgabe akzeptiert. Im oben erwähnten Beispiel verwenden Sie „BlobSource“ als Quelle und „SqlSink“ als Senke für die Kopieraktivität. Wenn Sie einen Kopiervorgang von Azure SQL-Datenbank zu Azure Blob Storage durchführen, verwenden Sie entsprechend SqlSource und BlobSink in der Kopieraktivität. Informationen zu den Eigenschaften von Kopieraktivitäten, die spezifisch für Azure Blob Storage sind, finden Sie im Abschnitt [Eigenschaften der Kopieraktivität](#copy-activity-properties). Ausführliche Informationen zur Verwendung eines Datenspeichers als Quelle oder Senke erhalten Sie, indem Sie im vorherigen Abschnitt auf den Link für Ihren Datenspeicher klicken.

Wenn Sie den Assistenten verwenden, werden automatisch JSON-Definitionen für diese Data Factory-Entitäten (verknüpfte Diensten, Datasets und die Pipeline) erstellt. Bei Verwendung von Tools und APIs (mit Ausnahme der .NET-API) definieren Sie diese Data Factory-Entitäten im JSON-Format.  Beispiele mit JSON-Definitionen für Data Factory-Entitäten für das Kopieren von Daten in und aus Azure Blob Storage finden Sie in diesem Artikel im Abschnitt [JSON-Beispiele](#json-examples-for-copying-data-to-and-from-blob-storage  ).

Die folgenden Abschnitte enthalten Details zu JSON-Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für Azure Blob Storage verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts
Es gibt zwei Arten von verknüpften Diensten, die Sie verwenden können, um einen Azure Storage mit einer Azure Data Factory zu verknüpfen. Sie lauten wie folgt: verknüpfter Dienst **AzureStorage** und verknüpfter Dienst **AzureStorageSas**. Dagegen bietet der mit Azure Storage SAS (Shared Access Signature) verknüpfte Dienst der Data Factory einen eingeschränkten bzw. zeitgebundenen Zugriff auf Azure-Speicher. Es gibt keine weitere Unterschiede zwischen diesen beiden verknüpften Diensten. Wählen Sie den verknüpften Dienst, der Ihren Anforderungen entspricht. Die folgenden Abschnitte bieten weitere Informationen zu diesen beiden verknüpften Diensten.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Dataset-Eigenschaften
Um ein Dataset zur Darstellung von Eingabe- oder Ausgabedaten in einem Azure Blob Storage anzugeben, legen Sie die Typeigenschaft des Datasets auf **AzureBlob** fest. Legen Sie die **linkedServiceName**-Eigenschaft des Datasets auf den Namen des mit Azure Storage oder Azure Storage SAS verknüpften Diensts fest.  Die Typeigenschaften des Datasets geben den **Blobcontainer** und den **Ordner** im Blobspeicher an.

Eine vollständige Liste der JSON-Abschnitte und -Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Erstellen von Datasets](data-factory-create-datasets.md). Abschnitte wie „structure“, „availability“ und „policy“ des JSON-Codes eines Datasets sind bei allen Dataset-Typen (Azure SQL, Azure-Blob, Azure-Tabelle usw.) ähnlich.

Data Factory unterstützt die folgenden CLS-konformen und auf .NET basierenden Typwerte für die Bereitstellung von Typinformationen in „structure“ für Schema-on-Read-Datenquellen wie Azure-Blob: Int16, Int32, Int64, Single, Double, Decimal, Byte[], Bool, String, Guid, Datetime, Datetimeoffset, Timespan. Data Factory führt beim Verschieben von Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher automatisch Typkonvertierungen durch.

Der Abschnitt **typeProperties** unterscheidet sich bei jeder Art von Dataset und enthält unter anderem Informationen zum Speicherort und Format der Daten im Datenspeicher. Der Abschnitt „typeProperties“ für ein Dataset des Typs **AzureBlob** hat die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| folderPath |Der Pfad zum Container und Ordner im Blobspeicher. Beispiel: myblobcontainer\myblobfolder\ |Ja |
| fileName |Der Name des Blobs. fileName ist optional, wobei seine Groß- und Kleinschreibung beachtet werden muss.<br/><br/>Wenn Sie einen Dateinamen angeben, funktioniert die Aktivität (einschließlich Kopieren) für das jeweilige Blob.<br/><br/>Wenn „fileName“ nicht angegeben ist, werden alle Blobs in folderPath für das Eingabedataset kopiert.<br/><br/>Wenn **fileName** nicht für ein Ausgabedataset und **preserveHierarchy** nicht in der Aktivitätssenke angegeben ist, hat der Name der generierten Datei das folgende Format: `Data.<Guid>.txt` (Beispiel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt). |Nein |
| partitionedBy |"partitionedBy" ist eine optionale Eigenschaft. "partitionedBy" kann genutzt werden, um einen dynamischen Wert für "folderPath" oder "fileName" für Zeitreihendaten anzugeben. Beispiel: "folderPath" kann für jedes stündliche Datenaufkommen parametrisiert werden. Im Abschnitt [Nutzen der Eigenschaft „partitionedBy“](#using-partitionedby-property) finden Sie Details und Beispiele. |Nein |
| format | Die folgenden Formattypen werden unterstützt: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** und **ParquetFormat**. Sie müssen die **type** -Eigenschaft unter „format“ auf einen dieser Werte festlegen. Weitere Informationen finden Sie in den Abschnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-Format](data-factory-supported-file-and-compression-formats.md#orc-format) und [Parquet-Format](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Wenn Sie **Dateien unverändert zwischen dateibasierten Speichern kopieren** möchten (binäre Kopie), können Sie den Formatabschnitt bei den Definitionen von Eingabe- und Ausgabedatasets überspringen. |Nein |
| compression | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Folgende Typen werden unterstützt: **GZip**, **Deflate**, **BZip2** und **ZipDeflate**. Folgende Ebenen werden unterstützt: **Optimal** und **Fastest**. Weitere Informationen finden Sie unter [Datei- und Komprimierungsformate in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nein |

### <a name="using-partitionedby-property"></a>Nutzen der partitionedBy-Eigenschaft
Wie im vorherigen Abschnitt erwähnt, können die **partitionedBy**-Eigenschaft, [Data Factory-Funktionen und Systemvariablen](data-factory-functions-variables.md) verwendet werden, um für Zeitreihendaten einen dynamischen Wert für folderPath und filename anzugeben.

In den Artikeln [Erstellen von Datasets](data-factory-create-datasets.md) und [Planung und Ausführung](data-factory-scheduling-and-execution.md) finden Sie weitere Informationen zu Zeitreihen-Datasets, Planung und Slices.

#### <a name="sample-1"></a>Beispiel 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Im obigen Beispiel wird {Slice} durch den Wert der Data Factory-Systemvariablen SliceStart ersetzt, die im Format (JJJJMMTTHH) angegeben ist. "SliceStart" bezieht sich auf die Startzeit des Slices. "folderPath" unterscheidet sich für jeden Slice. Beispiel: "wikidatagateway/wikisampledataout/2014100103" oder "wikidatagateway/wikisampledataout/2014100104".

#### <a name="sample-2"></a>Beispiel 2

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

Im Beispiel oben werden Jahr, Monat, Tag und Uhrzeit von SliceStart in separate Variablen extrahiert, die von den Eigenschaften „folderPath“ und „fileName“ verwendet werden.

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität
Eine vollständige Liste der Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabedatasets und Richtlinien sind für alle Arten von Aktivitäten verfügbar. Eigenschaften im Abschnitt **typeProperties** der Aktivität können dagegen je nach Aktivitätstyp variieren. Für die Kopieraktivität variieren die Eigenschaften je nach Art der Quellen und Senken. Wenn Sie Daten aus Azure Blob Storage verschieben, legen Sie den Quelltyp in der Kopieraktivität auf **BlobSource** fest. Wenn Sie hingegen Daten in Azure Blob Storage verschieben, legen Sie den Senkentyp in der Kopieraktivität auf **BlobSink** fest. Dieser Abschnitt enthält eine Liste der Eigenschaften, die von „BlobSource“ und „BlobSink“ unterstützt werden.

**BlobSource** unterstützt die folgenden Eigenschaften im Abschnitt **typeProperties**:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| recursive |Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. |True (Standardwert), False |Nein |

**BlobSink** unterstützt die folgenden Eigenschaften im Abschnitt **typeProperties**:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| copyBehavior |Definiert das Verhalten beim Kopieren, wenn die Quelle "BlobSource" oder "FileSystem" ist. |<b>PreserveHierarchy:</b> behält die Dateihierarchie im Zielordner bei. Der relative Pfad der Quelldatei zum Quellordner entspricht dem relativen Pfad der Zieldatei zum Zielordner.<br/><br/><b>FlattenHierarchy</b>: Alle Dateien aus dem Quellordner befinden sich in der ersten Ebene des Zielordners. Für die Zieldateien wird ein automatisch ein Name erzeugt. <br/><br/><b>MergeFiles:</b> führt alle Dateien aus dem Quellordner in einer Datei zusammen. Wenn der Datei-/Blob-Name angegeben wurde, entspricht der Name dem angegebenen Namen, andernfalls dem automatisch generierten Dateinamen. |Nein |

**BlobSource** unterstützt darüber hinaus zum Zweck der Abwärtskompatibilität die beiden folgenden Eigenschaften:

* **treatEmptyAsNull**: Gibt an, ob Null oder eine leere Zeichenfolge als NULL-Wert behandelt wird.
* **skipHeaderLineCount** : Gibt an, wie viele Zeilen übersprungen werden müssen. Nur anwendbar, wenn das Eingabedataset „TextFormat“ verwendet.

**BlobSink** unterstützt zum Zweck der Abwärtskompatibilität die folgende Eigenschaft:

* **blobWriterAddHeader**: Gibt an, ob beim Schreiben in ein Ausgabedataset ein Header mit Spaltendefinitionen hinzugefügt werden soll.

Datasets unterstützen jetzt die folgenden Eigenschaften, die die gleichen Funktionen implementieren: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

Die folgende Tabelle enthält Anweisungen zur Verwendung der neuen Dataset-Eigenschaften anstelle dieser Blobquellen-/Senkeneigenschaften.

| Eigenschaft der Kopieraktivität | Dataset-Eigenschaft |
|:--- |:--- |
| „skipHeaderLineCount“ für „BlobSource“ |„skipLineCount“ und „firstRowAsHeader“. Zeilen werden zunächst übersprungen. Anschließend wird die erste Zeile als Header gelesen. |
| „treatEmptyAsNull“ für „BlobSource“ |„treatEmptyAsNull“ für Eingabedataset |
| „blobWriterAddHeader“ für „BlobSink“ |„firstRowAsHeader“ für Ausgabedataset |

Ausführliche Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Angeben von „TextFormat“](data-factory-supported-file-and-compression-formats.md#text-format) .

### <a name="recursive-and-copybehavior-examples"></a>Beispiele für "recursive" und "copyBehavior"
Dieser Abschnitt beschreibt das resultierende Verhalten des Kopiervorgangs für verschiedene Kombinationen von rekursiven und CopyBehavior-Werten.

| recursive | copyBehavior | Resultierendes Verhalten |
| --- | --- | --- |
| true |preserveHierarchy |Für den Quellordner „Ordner1“ mit der folgenden Struktur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5<br/><br/>wird der Zielordner „Ordner1“ mit der gleichen Struktur erstellt wie die Quelle<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5 |
| true |flattenHierarchy |Für den Quellordner „Ordner1“ mit der folgenden Struktur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5<br/><br/>wird der Zielordner „Ordner1“ mit der folgenden Struktur erstellt: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei5 |
| true |mergeFiles |Für den Quellordner „Ordner1“ mit der folgenden Struktur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5<br/><br/>wird der Zielordner „Ordner1“ mit der folgenden Struktur erstellt: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp; Inhalte von Datei1 + Datei2 + Datei3 + Datei4 + Datei5 werden in einer Datei mit einem automatisch generierten Namen zusammengeführt. |
| false |preserveHierarchy |Für den Quellordner „Ordner1“ mit der folgenden Struktur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5<br/><br/>wird der Zielordner „Ordner1“ mit der folgenden Struktur erstellt:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/><br/><br/>Unterordner1 mit Datei3, Datei4 und Datei5 wird nicht übernommen. |
| false |flattenHierarchy |Für den Quellordner „Ordner1“ mit der folgenden Struktur:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5<br/><br/>wird der Zielordner „Ordner1“ mit der folgenden Struktur erstellt:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei2<br/><br/><br/>Unterordner1 mit Datei3, Datei4 und Datei5 wird nicht übernommen. |
| false |mergeFiles |Für den Quellordner „Ordner1“ mit der folgenden Struktur:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5<br/><br/>wird der Zielordner „Ordner1“ mit der folgenden Struktur erstellt:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Inhalte von Datei1 + Datei2 werden zu einer Datei mit einem automatisch generierten Namen zusammengeführt. Automatisch generierter Name für Datei1<br/><br/>Unterordner1 mit Datei3, Datei4 und Datei5 wird nicht übernommen. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>Exemplarische Vorgehensweise: Verwenden des Kopier-Assistenten, um Daten in/aus Blob Storage zu kopieren
Es wird nun gezeigt, wie Daten schnell in einen/aus einem Azure Blob Storage kopiert werden können. In dieser exemplarischen Vorgehensweise haben sowohl der Quell- als auch der Zieldatenspeicher den Typ „Azure Blob Storage“. In der Pipeline in dieser exemplarischen Vorgehensweise werden Daten aus einem Ordner in einen anderen Ordner im selben BLOB-Container kopiert. Diese exemplarische Vorgehensweise ist bewusst einfach gehalten, um Einstellungen oder Eigenschaften für den Fall zu veranschaulichen, dass Blob Storage als Quelle oder Senke verwendet wird.

### <a name="prerequisites"></a>Voraussetzungen
1. Erstellen Sie ein allgemein verwendbares **Azure Storage-Konto** (Azure-Speicherkonto), wenn Sie noch keines haben. In dieser exemplarischen Vorgehensweise verwenden Sie den Blob Storage sowohl als **Quell**- als auch als **Ziel** datenspeicher. Wenn Sie kein Azure Storage-Konto haben, finden Sie im Artikel [Erstellen eines Speicherkontos](../../storage/common/storage-account-create.md) Schritte zum Erstellen eines Azure Storage-Kontos.
2. Erstellen Sie einen BLOB-Container namens **adfblobconnector** im Speicherkonto.
4. Erstellen Sie einen Ordner namens **input** im **adfblobconnector**-Container.
5. Erstellen Sie eine Datei namens **emp.txt** mit dem folgenden Inhalt, und laden Sie diese Datei in den Ordner **input** hoch, indem Sie ein Tool wie [Azure Storage-Explorer](https://azurestorageexplorer.codeplex.com/) verwenden.
    ```json
    John, Doe
    Jane, Doe
    ```

### <a name="create-the-data-factory"></a>Erstellen der Data Factory
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie links oben auf **Ressource erstellen** und anschließend auf **Intelligence + Analyse** und **Data Factory**.
3. Gehen Sie im Bereich **Neue Data Factory** wie folgt vor:  
    1. Geben Sie **ADFBlobConnectorDF** für **Name** ein. Der Name der Azure Data Factory muss global eindeutig sein. Sollte der Fehler `*Data factory name “ADFBlobConnectorDF” is not available` angezeigt werden, ändern Sie den Namen der Data Factory (etwa „IhrNameADFBlobConnectorDF“), und wiederholen Sie den Vorgang. Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](data-factory-naming-rules.md) .
    2. Wählen Sie Ihr Azure- **Abonnement** aus.
    3. Wählen Sie für „Ressourcengruppe“ die Option **Vorhandene verwenden** aus, um eine vorhandene Ressourcengruppe auszuwählen, oder wählen Sie **Erstellen** aus, um einen Namen für eine Ressourcengruppe einzugeben.
    4. Wählen Sie einen **Standort** für die Data Factory aus.
    5. Aktivieren Sie unten auf dem Blatt das Kontrollkästchen **An Dashboard anheften**.
    6. Klicken Sie auf **Erstellen**.
3. Nach Abschluss der Erstellung wird das Blatt **Data Factory** wie in der folgenden Abbildung dargestellt angezeigt:  ![Data Factory-Startseite](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>Kopier-Assistent
1. Klicken Sie auf der Data Factory-Startseite auf die Kachel **Daten kopieren**, um den **Assistenten zum Kopieren von Daten** in einer eigenen Registerkarte zu starten.  

    > [!NOTE]
    > Wenn Sie feststellen, dass der Webbrowser bei der Autorisierung hängen bleibt, deaktivieren Sie die Einstellung **Cookies und Websitedaten von Drittanbietern blockieren**, oder lassen Sie die Einstellung aktiviert, und erstellen Sie eine Ausnahme für **login.microsoftonline.com**. Versuchen Sie anschließend erneut, den Assistenten zu starten.
2. Auf der Seite **Eigenschaften**:
    1. Geben Sie **CopyPipeline** für **Aufgabenname** ein. Der Aufgabenname ist der Name der Pipeline in Ihrer Data Factory.
    2. Geben Sie eine **Beschreibung**  für die Aufgabe ein (optional).
    3. Übernehmen Sie für **Aufgabenhäufigkeit oder Aufgabenzeitplan** die Option **Regelmäßig ausführen nach Zeitplan**. Soll diese Aufgabe nicht wiederholt nach Zeitplan, sondern nur einmal ausgeführt werden, aktivieren Sie die Option **Einmal jetzt ausführen**. Wenn Sie **Einmal jetzt ausführen** aktivieren, wird eine [einmalige Pipeline](data-factory-create-pipelines.md#onetime-pipeline) (OneTime-Pipeline) erstellt.
    4. Übernehmen Sie die Einstellungen für **Wiederkehrendes Muster**. Diese Aufgabe wird täglich zwischen der Start- und der Endzeit ausgeführt, die Sie im nächsten Schritt angeben.
    5. Ändern Sie den **Startzeitpunkt** in **21.04.2017**.
    6. Ändern Sie den **Endzeitpunkt** in **25.04.2017**. Sie können das jeweilige Datum auch direkt eingeben, statt den Kalender zu durchlaufen.
    8. Klicken Sie auf **Weiter**.
        ![Kopiertool – Seite „Eigenschaften“](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png)
3. Klicken Sie auf der Seite **Quelldatenspeicher** auf die Kachel **Azure Blob Storage**. Sie können diese Seite verwenden, um den Quelldatenspeicher für die Kopieraufgabe anzugeben. Sie können einen verknüpften Datenspeicherdienst verwenden oder einen neuen Datenspeicher angeben. Wählen Sie zum Verwenden eines vorhandenen verknüpften Diensts die Option **AUS VORHANDENEN VERKNÜPFTEN DIENSTEN** aus, und wählen Sie den richtigen verknüpften Dienst aus.
    ![Kopiertool – Seite „Quelldatenspeicher“](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. Auf der Seite **Azure Blob Storage-Konto angeben** :
    1. Übernehmen Sie den automatisch generierten Namen für **Verbindungsname**. Der Verbindungsname ist der Name des verknüpften Diensts vom Typ „Azure Storage“.
    2. Überprüfen Sie, ob unter **Kontoauswahlmethode** die Option **Über Azure-Abonnements** ausgewählt ist.
    3. Wählen Sie Ihr Azure-Abonnement aus, oder übernehmen Sie **Alle auswählen** für **Azure-Abonnement**.
    4. Wählen Sie in der Liste mit den **Azure-Speicherkonten**, die im ausgewählten Abonnement verfügbar sind, ein Azure-Speicherkonto aus. Sie können sich auch für das manuelle Eingeben von Speicherkontoeinstellungen entscheiden, indem Sie unter **Kontoauswahlmethode** die Option **Manuell eingeben** auswählen.
    5. Klicken Sie auf **Weiter**.  
        ![Kopiertool – Azure Blob Storage-Konto angeben](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. Auf der Seite **Eingabedatei oder -ordner auswählen** :
    1. Doppelklicken Sie auf **adfblobcontainer**.
    2. Wählen Sie **input** aus, und klicken Sie auf **Auswählen**. In dieser exemplarischen Vorgehensweise wählen Sie den Ordner „input“ aus. Sie könnten stattdessen die Datei „emp.txt“ im Ordner auswählen.
        ![Kopiertool: Auswählen der Eingabedatei oder des Eingabeordners 1](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. Auf der Seite für **Eingabedatei oder -ordner auswählen**:
    1. Vergewissern Sie sich, dass **Datei oder Ordner** auf **adfblobconnector/input** festgelegt ist. Wenn sich die Dateien in Unterordnern befinden, z. B. „2017/04/01“, „2017/04/02“ usw., geben Sie „adfblobconnector/input/{year}/{month}/{day}“ für „Datei oder Ordner“ ein. Wenn Sie im Textfeld die TAB-TASTE drücken, werden drei Dropdownlisten angezeigt, in denen die Formate für Jahr (yyyy), Monat (MM) und Tag (dd) ausgewählt werden können.
    2. Aktivieren Sie nicht die Option **Dateien rekursiv kopieren**. Aktivieren Sie diese Option, wenn Sie die Ordner rekursiv durchlaufen möchten, um die in das Ziel zu kopierenden Dateien auszuwählen.
    3. Aktivieren Sie nicht die **Binärkopie**. Aktivieren Sie diese Option, um die Quelldatei als Binärkopie in das Ziel zu kopieren. Aktivieren Sie diese Option nicht für diese exemplarische Vorgehensweise, damit auf den nächsten Seiten weitere Optionen angezeigt werden.
    4. Vergewissern Sie sich, dass der **Komprimierungstyp** auf **Kein** festgelegt ist. Wählen Sie einen Wert für diese Option aus, wenn die Quelldateien in einem der unterstützten Formate komprimiert sind.
    5. Klicken Sie auf **Weiter**.
    ![Kopiertool: Auswählen der Eingabedatei oder des Eingabeordners 2](./media/data-factory-azure-blob-connector/chose-input-file-folder.png)
7. Auf der Seite **File format settings** (Dateiformateinstellungen) werden die Trennzeichen und das Schema angezeigt. Diese Informationen werden vom Assistenten beim Analysieren der Datei automatisch erkannt.
    1. Bestätigen Sie die folgenden Optionen:  
        a. Das **Dateiformat** ist auf **Textformat** festgelegt. In der Dropdownliste werden alle unterstützten Formate angezeigt. Beispiel: JSON, Avro, ORC, Parquet.
       b. Das **Spaltentrennzeichen** ist auf `Comma (,)` festgelegt. Die weiteren Spaltentrennzeichen, die von Data Factory unterstützt werden, werden in der Dropdownliste angezeigt. Sie können auch ein benutzerdefiniertes Trennzeichen angeben.
       c. Das **Zeilentrennzeichen** ist auf `Carriage Return + Line feed (\r\n)` festgelegt. Die weiteren Zeilentrennzeichen, die von Data Factory unterstützt werden, werden in der Dropdownliste angezeigt. Sie können auch ein benutzerdefiniertes Trennzeichen angeben.
       d. Die **Anzahl zu überspringender Zeilen** ist auf **0** festgelegt. Wenn Sie möchten, dass einige Zeilen am Anfang der Datei übersprungen werden sollen, geben Sie hier die Anzahl ein.
       e. Die Option **Die erste Datenzeile enthält Spaltennamen** ist nicht aktiviert. Wenn die Quelldateien in der ersten Zeile Spaltennamen enthalten, aktivieren Sie diese Option.
       f. Die Option **Leeren Spaltenwert als Nullwert behandeln** ist aktiviert.
    2. Erweitern Sie **Erweiterte Einstellungen**, um die verfügbaren erweiterten Optionen anzuzeigen.
    3. Unten auf der Seite finden Sie die **Vorschau** von Daten aus der Datei „emp.txt“.
    4. Klicken Sie auf die unten angezeigte Registerkarte **SCHEMA**, um sich das Schema anzusehen, das der Kopier-Assistent durch Auswerten der Daten in der Quelldatei abgeleitet hat.
    5. Klicken Sie auf **Weiter**, nachdem Sie die Trennzeichen und Vorschaudaten geprüft haben.
    ![Kopiertool – Dateiformateinstellungen](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)
8. Wählen Sie auf der Seite **Zieldatenspeicher** die Option **Azure Blob Storage** aus, und klicken Sie auf **Weiter**. In dieser exemplarischen Vorgehensweise verwenden Sie den Azure Blob Storage sowohl als Quell- als auch als Zieldatenspeicher.  
    ![Kopiertool – Zieldatenspeicher auswählen](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. Auf der Seite **Azure Blob Storage-Konto angeben**:  
    1. Geben Sie im Feld **Verbindungsname** den Text **AzureStorageLinkedService** ein.
    2. Überprüfen Sie, ob unter **Kontoauswahlmethode** die Option **Über Azure-Abonnements** ausgewählt ist.
    3. Wählen Sie Ihr Azure- **Abonnement** aus.
    4. Wählen Sie Ihr Azure Storage-Konto aus.
    5. Klicken Sie auf **Weiter**.
10. Auf der Seite für **Ausgabedatei oder -ordner auswählen**:  
    1. Geben Sie **Ordnerpfad** als **adfblobconnector/output/{year}/{month}/{day}** an. Drücken Sie die **TAB-TASTE**.
    1. Wählen Sie für **Jahr** die Option **yyyy** aus.
    1. Vergewissern Sie sich, dass **Monat** auf **MM** festgelegt ist.
    1. Vergewissern Sie sich, dass **Tag** auf **dd** festgelegt ist.
    1. Vergewissern Sie sich, dass der **Komprimierungstyp** auf **Kein** festgelegt ist.
    1. Vergewissern Sie sich, dass das **Kopierverhalten** auf **Dateien zusammenführen** festgelegt ist. Gibt es bereits eine Ausgabedatei mit demselben Namen, wird der neue Inhalt am Ende dieser Datei hinzugefügt.
    1. Klicken Sie auf **Weiter**.
       ![Kopiertool – Ausgabedatei- oder -ordner auswählen](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. Überprüfen Sie auf der Seite **Dateiformateinstellungen** die Einstellungen, und klicken Sie auf **Weiter**. Eine der weiteren Optionen an dieser Stelle besteht darin, eine Kopfzeile (Header) zu der Ausgabedatei hinzuzufügen. Wenn Sie diese Option aktivieren, wird eine Kopfzeile hinzugefügt, die die Namen der Spalten aus dem Schema der Quelle enthält. Sie können die Standardspaltennamen ändern, wenn Sie das Schema für die Quelle anzeigen. Beispielsweise könnten Sie den Namen der ersten Spalte in „Vorname“ und den Namen der zweiten Spalte in „Nachname“ ändern. Die Ausgabedatei wird dann mit einer Kopfzeile generiert, die diese Namen als Spaltennamen enthält.
    ![Kopiertool – Dateiformateinstellungen für das Ziel](media/data-factory-azure-blob-connector/file-format-destination.png)
12. Vergewissern Sie sich auf der Seite **Leistungseinstellungen**, dass **Cloudeinheiten** und **Parallele Kopien** auf **Auto** festgelegt sind, und klicken Sie auf „Weiter“. Einzelheiten zu diesen Einstellungen finden Sie im [Handbuch zur Leistung und Optimierung der Kopieraktivität](data-factory-copy-activity-performance.md#parallel-copy).
    ![Kopiertool – Leistungseinstellungen](media/data-factory-azure-blob-connector/copy-performance-settings.png)
14. Überprüfen Sie auf der Seite **Zusammenfassung** alle Einstellungen (Aufgabeneigenschaften, Einstellungen für Quelle und Ziel sowie Kopiereinstellungen), und klicken Sie auf **Weiter**.
    ![Kopiertool – Seite „Zusammenfassung“](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. Überprüfen Sie die Informationen auf der Seite **Zusammenfassung**, und klicken Sie auf **Fertig stellen**. Der Assistent erstellt zwei verknüpfte Dienste, zwei Datasets (Eingabe und Ausgabe) und eine Pipeline in der Data Factory erstellt (von der aus Sie den Kopier-Assistenten gestartet haben).
    ![Kopiertool – Seite „Bereitstellung“](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>Überwachen der Pipeline (Kopieraufgabe)

1. Klicken Sie auf der Seite **Bereitstellung** auf den Link `Click here to monitor copy pipeline`.
2. Nun sollte die **Anwendung für Überwachen und Verwalten** auf einer separaten Registerkarte angezeigt werden.  ![Anwendung für Überwachen und Verwalten](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. Ändern Sie oben die **Start** zeit in `04/19/2017` und die **End** zeit in `04/27/2017`, und klicken Sie dann auf **Anwenden**.
4. In der Liste **AKTIVITÄTSFENSTER** sollten nun fünf Aktivitätsfenster zu sehen sein. Die Zeiten unter **Fensterstart** sollten alle Tage ab der Pipelinestart- bis zur Pipelinendzeit abdecken.
5. Klicken Sie mehrmals auf die Schaltfläche **Aktualisieren** für die Liste **AKTIVITÄTSFENSTER**, bis der Status jedes Aktivitätsfensters auf „Bereit“ festgelegt ist.
6. Vergewissern Sie sich jetzt, dass die Ausgabedateien im Container „adfblobconnector“ in dessen Ordner „output“ generiert wurden. Für den Ordner „output“ sollte die folgende Ordnerstruktur angezeigt werden:
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25
    ```
   Ausführliche Informationen zum Überwachen und Verwalten von Data Factorys finden Sie unter [Überwachen und Verwalten von Azure Data Factory-Pipelines](data-factory-monitor-manage-app.md).

### <a name="data-factory-entities"></a>Data Factory-Entitäten
Kehren Sie jetzt zur Registerkarte mit der Data Factory-Startseite zurück. Sie können sehen, dass Ihre Data Factory nun zwei verknüpfte Dienste, zwei Datasets und eine Pipeline enthält.

![Data Factory-Startseite mit Entitäten](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Klicken Sie auf **Verfassen und bereitstellen**, um den Data Factory-Editor zu starten.

![Data Factory Editor](media/data-factory-azure-blob-connector/data-factory-editor.png)

Sie sollten die folgenden Data Factory-Entitäten in Ihrer Data Factory sehen:

- Zwei verknüpfte Dienste. Einer für die Quelle und der andere für das Ziel. In dieser exemplarischen Vorgehensweise verweisen beide verknüpften Dienste auf dasselbe Azure Storage-Konto.
- Zwei Datasets. Ein Eingabedataset und ein Ausgabedataset. In dieser exemplarischen Vorgehensweise wird für beide Datasets derselbe BLOB-Container verwendet, sie befinden sich jedoch in unterschiedlichen Ordnern („input“ und „output“).
- Eine Pipeline. Die Pipeline enthält eine Kopieraktivität, in der eine Blobquelle und eine Blobsenke verwendet werden, um Daten aus einem Azure-Blobspeicherort in einen anderen Azure-Blobspeicherort zu kopieren.

Die folgenden Abschnitte enthalten weitere Informationen zu diesen Entitäten.

#### <a name="linked-services"></a>Verknüpfte Dienste
Es sollten zwei verknüpfte Dienste angezeigt werden. Einer für die Quelle und der andere für das Ziel. In dieser exemplarischen Vorgehensweise unterscheiden sich die beiden Definitionen nur in den Namen. Die **type**-Eigenschaft des verknüpften Diensts ist auf **AzureStorage** festgelegt. Die wichtigste Eigenschaft der Definition des verknüpften Diensts ist die **connectionString**-Eigenschaft, die von Data Factory verwendet wird, um zur Laufzeit eine Verbindung mit Ihrem Azure Storage-Konto herzustellen. Ignorieren Sie die Definition der „hubName“-Eigenschaft.

##### <a name="source-blob-storage-linked-service"></a>Verknüpfter Dienst für den Quell-Blobspeicher
```json
{
    "name": "Source-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

##### <a name="destination-blob-storage-linked-service"></a>Verknüpfter Dienst für den Ziel-Blobspeicher

```json
{
    "name": "Destination-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

Weitere Informationen zum verknüpften Dienst für Azure Storage finden Sie im Abschnitt [Eigenschaften des verknüpften Diensts](#linked-service-properties).

#### <a name="datasets"></a>Datasets
Es gibt zwei Datasets: ein Eingabedataset und ein Ausgabedataset. Der Datasettyp ist für beide auf **AzureBlob** festgelegt.

Das Eingabedataset verweist auf den Ordner **input** im BLOB-Container **adfblobconnector**. Die **external**-Eigenschaft für dieses Dataset ist auf **true** festgelegt, weil die Daten nicht von der Pipeline mit der Kopieraktivität erstellt werden, für die dieses Dataset als Eingabe fungiert.

Das Ausgabedataset verweist auf den Ordner **output** im selben BLOB-Container. Für das Ausgabedataset werden außerdem das Jahr, der Monat und der Tag der **SliceStart**-Systemvariablen verwendet, um den Pfad für die Ausgabedatei dynamisch auszuwerten. Eine Liste mit den Funktionen und Systemvariablen, die von Data Factory unterstützt werden, finden Sie unter [Data Factory – Funktionen und Systemvariablen](data-factory-functions-variables.md). Die **external**-Eigenschaft ist auf **false** (Standardwert) festgelegt, weil dieses Dataset von der Pipeline erstellt wird.

Weitere Informationen zu Eigenschaften, die von Azure-Blobdataset unterstützt werden, finden Sie im Abschnitt [Dataset-Eigenschaften](#dataset-properties).

##### <a name="input-dataset"></a>Eingabedataset

```json
{
    "name": "InputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Source-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/input/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

##### <a name="output-dataset"></a>Ausgabedataset

```json
{
    "name": "OutputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Destination-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/output/{year}/{month}/{day}",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
            "partitionedBy": [
                { "name": "year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }
            ]
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

#### <a name="pipeline"></a>Pipeline
Die Pipeline hat nur eine Aktivität. Die **type**-Eigenschaft der Aktivität ist auf **Copy** festgelegt. In den „type“-Eigenschaften für die Aktivität gibt es zwei Abschnitte, einen für die Quelle und den anderen für die Senke. Der Quelltyp (source type) ist auf **BlobSource** festgelegt, weil die Aktivität Daten aus einem Blob Storage kopiert. Der Senkentyp (sink type) ist auf **BlobSink** festgelegt, weil die Aktivität Daten in einen Blob Storage kopiert. Die Kopieraktivität verwendet „InputDataset-z4y“ als Eingabe und „OutputDataset-z4y“ als Ausgabe.

Weitere Informationen zu Eigenschaften, die von „BlobSource“ und „BlobSink“ unterstützt werden, finden Sie im Abschnitt [Eigenschaften der Kopieraktivität](#copy-activity-properties).

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "MergeFiles",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-z4y"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-z4y"
                    }
                ],
                "policy": {
                    "timeout": "1.00:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 3,
                    "longRetry": 0,
                    "longRetryInterval": "00:00:00"
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "Activity-0-Blob path_ adfblobconnector_input_->OutputDataset-z4y"
            }
        ],
        "start": "2017-04-21T22:34:00Z",
        "end": "2017-04-25T05:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>JSON-Beispiele zum Kopieren von Daten in und aus Blob Storage
Die folgenden Beispiele zeigen JSON-Beispieldefinitionen, die Sie zum Erstellen einer Pipeline mit [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) oder [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) verwenden können. Sie zeigen Ihnen das Kopieren von Daten aus Azure Blob Storage in Azure SQL-Datenbank (und umgekehrt). Daten können jedoch mithilfe der Kopieraktivität in Azure Data Factory **direkt** aus beliebigen Quellen in die [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) aufgeführten Senken kopiert werden.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>JSON-Beispiel: Kopieren von Daten aus Blob Storage in SQL-Datenbank
Dieses Beispiel zeigt Folgendes:

1. Einen verknüpften Dienst des Typs [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties)
2. Einen verknüpften Dienst des Typs [AzureStorage](#linked-service-properties)
3. Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](#dataset-properties)
4. Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties)
5. Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [BlobSource](#copy-activity-properties) und [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties) verwendet

Im Beispiel werden Zeitreihendaten aus einem Azure-Blob stündlich in eine Azure SQL-Tabelle kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

**Mit Azure SQL verknüpfter Dienst:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Mit Azure Storage verknüpfter Dienst:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure Data Factory unterstützt zwei Arten von verknüpften Azure Storage-Diensten: **AzureStorage** und **AzureStorageSas**. Für den ersten geben Sie die Verbindungszeichenfolge an, die den Kontoschlüssel enthält. Für den zweiten geben Sie den SAS-URI (Shared Access Signature) an. Weitere Informationen finden Sie unter [Verknüpfte Dienste](#linked-service-properties).

**Azure-Blob-Eingabedataset:**

Daten werden stündlich aus einem neuen Blob übernommen (frequency: hour, interval: 1). Ordnerpfad und Dateiname des Blobs werden basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Der Ordnerpfad verwendet die Bestandteile Jahr, Monat und Tag der Startzeit, und der Dateiname verwendet die Stunde der Startzeit. Die Festlegung von „external“ auf „true“ teilt dem Data Factory-Dienst mit, dass diese Tabelle für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Azure SQL-Ausgabedataset:**

Das Beispiel kopiert Daten in eine Tabelle namens „MyTable“ in Azure SQL-Datenbank. Erstellen Sie die Tabelle in der SQL-Datenbank mit der gleichen Anzahl von Spalten, die Sie in der Blob-CSV-Datei erwarten. Neue Zeilen werden der Tabelle stündlich hinzugefügt.

```json
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Eine Kopieraktivität in einer Pipeline mit Blobquelle und SQL-Senke:**

Die Pipeline enthält eine Kopieraktivität, die für die Verwendung der Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **BlobSource** und der Typ **sink** auf **SqlSink** festgelegt.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>JSON-Beispiel: Kopieren von Daten aus Azure SQL in ein Azure-Blob
Dieses Beispiel zeigt Folgendes:

1. Einen verknüpften Dienst des Typs [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties)
2. Einen verknüpften Dienst des Typs [AzureStorage](#linked-service-properties)
3. Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties)
4. Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](#dataset-properties)
5. Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) und [BlobSink](#copy-activity-properties) verwendet

Im Beispiel werden Zeitreihendaten aus einer Azure SQL-Tabelle stündlich in einen Azure-Blob kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

**Mit Azure SQL verknüpfter Dienst:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Mit Azure Storage verknüpfter Dienst:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure Data Factory unterstützt zwei Arten von verknüpften Azure Storage-Diensten: **AzureStorage** und **AzureStorageSas**. Für den ersten geben Sie die Verbindungszeichenfolge an, die den Kontoschlüssel enthält. Für den zweiten geben Sie den SAS-URI (Shared Access Signature) an. Weitere Informationen finden Sie unter [Verknüpfte Dienste](#linked-service-properties).

**Azure SQL-Eingabedataset:**

Im Beispiel wird vorausgesetzt, dass Sie die Tabelle "MyTable" in Azure SQL erstellt haben, die für Zeitreihendaten eine Spalte namens "timestampcolumn" enthält.

Durch Festlegen von „external“ auf „true“ wird dem Data Factory-Dienst mitgeteilt, dass die Tabelle für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

```json
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Azure-Blob-Ausgabedataset:**

Daten werden stündlich in ein neues Blob geschrieben (frequency: hour, interval: 1). Der Ordnerpfad des Blobs wird basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Im Ordnerpfad werden Jahr, Monat, Tag und die Stundenteile der Startzeit verwendet.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
      "partitionedBy": [
        {
          "name": "Year",
          "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Eine Kopieraktivität in einer Pipeline mit einer SQL-Quelle und einer Blobsenke:**

Die Pipeline enthält eine Kopieraktivität, die für die Verwendung der Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **SqlSource** und der Typ **sink** auf **BlobSink** festgelegt. Die für die **SqlReaderQuery** -Eigenschaft angegebene SQL-Abfrage wählt die Daten der letzten Stunde zum Kopieren aus.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

> [!NOTE]
> Weitere Informationen zum Zuordnen von Spalten im Quelldataset zu Spalten im Senkendataset finden Sie unter [Zuordnen von Datasetspalten in Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Leistung und Optimierung
Der Artikel [Handbuch zur Leistung und Optimierung der Kopieraktivität](data-factory-copy-activity-performance.md) beschreibt wichtige Faktoren, die sich auf die Leistung der Datenverschiebung (Kopieraktivität) in Azure Data Factory auswirken, sowie verschiedene Möglichkeiten zur Leistungsoptimierung.
