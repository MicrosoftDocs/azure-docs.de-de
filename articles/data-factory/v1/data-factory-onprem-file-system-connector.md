---
title: Kopieren von Daten in ein bzw. aus einem Dateisystem mithilfe von Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, wie Daten mit Azure Data Factory in ein und aus einem lokalen Dateisystem kopiert werden.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: ce19f1ae-358e-4ffc-8a80-d802505c9c84
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 40086924731876dc44d9651ca46814149dba52f0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66122453"
---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Kopieren von Daten in ein und aus einem lokalen Dateisystem mit Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-onprem-file-system-connector.md)
> * [Version 2 (aktuelle Version)](../connector-file-system.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [Dateisystemconnector in V2](../connector-file-system.md).


In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten in ein bzw. aus einem lokalen Dateisystem zu kopieren. Dieser Artikel baut auf dem Artikel zu [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit der Kopieraktivität bietet.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Unterstützte Szenarien
Sie können Daten **aus einem lokalen Dateisystem** in die folgenden Datenspeicher kopieren:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Sie können Daten aus den folgenden Datenspeichern **in ein lokales Dateisystem** kopieren:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Bei der Kopieraktivität wird die Quelldatei nicht gelöscht, nachdem sie erfolgreich in das Ziel kopiert wurde. Wenn Sie die Quelldatei nach dem erfolgreichen Kopieren löschen müssen, erstellen Sie eine benutzerdefinierte Aktivität, um die Datei zu löschen, und verwenden Sie die Aktivität in der Pipeline.

## <a name="enabling-connectivity"></a>Herstellen der Verbindung
Data Factory unterstützt das Herstellen einer Verbindung mit dem lokalen Dateisystem über das **Datenverwaltungsgateway**. Sie müssen das Datenverwaltungsgateway in Ihrer lokalen Umgebung installieren, damit der Data Factory-Dienst Verbindungen mit unterstützten lokalen Datenspeichern herstellen kann, einschließlich des Dateisystems. Weitere Informationen zum Datenverwaltungsgateway und eine schrittweise Anleitung zum Einrichten des Gateways finden Sie im Artikel [Verschieben von Daten zwischen lokalen Quellen und der Cloud mit dem Datenverwaltungsgateway](data-factory-move-data-between-onprem-and-cloud.md). Abgesehen vom Datenverwaltungsgateway müssen keine anderen Binärdateien installiert werden, um die Kommunikation mit dem lokalen Dateisystem zu ermöglichen. Sie müssen das Datenverwaltungsgateway auch dann installieren und verwenden, wenn das Dateisystem auf einem virtuellen Azure IaaS-Computer vorliegt. Ausführliche Informationen zum Gateway finden Sie unter [Datenverwaltungsgateway](data-factory-data-management-gateway.md).

Um eine Linux-Dateifreigabe zu verwenden, installieren Sie [Samba](https://www.samba.org/) auf Ihrem Linux-Server, und installieren Sie das Datenverwaltungsgateway auf einem Windows-Server. Das Installieren des Datenverwaltungsgateways auf einem Linux-Server wird nicht unterstützt.

## <a name="getting-started"></a>Erste Schritte
Sie können eine Pipeline mit einer Kopieraktivität erstellen, die Daten mithilfe verschiedener Tools/APIs in ein und aus einem Dateisystem verschiebt.

Am einfachsten erstellen Sie eine Pipeline mit dem **Kopier-Assistenten**. Eine Schritt-für-Schritt-Anleitung finden Sie im [Tutorial: Erstellen einer Pipeline mit dem Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md) finden Sie eine kurze exemplarische Vorgehensweise zum Erstellen einer Pipeline mithilfe des Assistenten zum Kopieren von Daten.

Sie können auch die folgenden Tools zum Erstellen einer Pipeline verwenden: **Azure-Portal**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-Vorlagen**, **.NET-API** und **REST-API**. Im [Tutorial zur Kopieraktivität](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) finden Sie detaillierte Anweisungen, wie Sie eine Pipeline mit einer Kopieraktivität erstellen können.

Unabhängig davon, ob Sie Tools oder APIs verwenden, führen Sie die folgenden Schritte aus, um eine Pipeline zu erstellen, die Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher verschiebt:

1. Eine **Data Factory**. Eine Data Factory kann eine oder mehrere Pipelines enthalten.
2. Erstellen **verknüpfter Dienste** zum Verknüpfen von Eingabe- und Ausgabedatenspeichern mit Ihrer Data Factory. Wenn Sie beispielsweise Daten aus Azure Blob Storage in ein lokales Dateisystem kopieren, erstellen Sie zwei verknüpfte Dienste, um Ihr lokales Dateisystem und das Azure Storage-Konto mit Ihrer Data Factory zu verknüpfen. Informationen zu Eigenschaften von verknüpften Diensten, die spezifisch für ein lokales Dateisystem sind, finden Sie im Abschnitt [Eigenschaften des verknüpften Diensts](#linked-service-properties).
3. Erstellen von **Datasets** zur Darstellung von Eingabe- und Ausgabedaten für den Kopiervorgang. Im Beispiel, das im letzten Schritt erwähnt wurde, erstellen Sie ein Dataset, um den Blobcontainer und den Ordner mit den Eingabedaten anzugeben. Außerdem erstellen Sie noch ein weiteres Dataset, um den Ordner und Dateinamen (optional) in Ihrem Dateisystem anzugeben. Informationen zu Dataset-Eigenschaften, die spezifisch für ein lokales Dateisystem sind, finden Sie im Abschnitt [Dataset-Eigenschaften](#dataset-properties).
4. Erstellen einer **Pipeline** mit einer Kopieraktivität, die ein Dataset als Eingabe und ein Dataset als Ausgabe akzeptiert. Im oben erwähnten Beispiel verwenden Sie BlobSource als Quelle und FileSystemSink als Senke für die Kopieraktivität. Wenn Sie einen Kopiervorgang aus dem lokalen Dateisystem nach Azure Blob Storage durchführen, verwenden Sie entsprechend FileSystemSource und BlobSink in der Kopieraktivität. Informationen zu den Eigenschaften von Kopieraktivitäten, die spezifisch für das lokale Dateisystem sind, finden Sie im Abschnitt [Eigenschaften der Kopieraktivität](#copy-activity-properties). Ausführliche Informationen zur Verwendung eines Datenspeichers als Quelle oder Senke erhalten Sie, indem Sie im vorherigen Abschnitt auf den Link für Ihren Datenspeicher klicken.

Wenn Sie den Assistenten verwenden, werden automatisch JSON-Definitionen für diese Data Factory-Entitäten (verknüpfte Diensten, Datasets und die Pipeline) erstellt. Bei Verwendung von Tools und APIs (mit Ausnahme der .NET-API) definieren Sie diese Data Factory-Entitäten im JSON-Format.  Beispiele mit JSON-Definitionen für Data Factory-Entitäten für das Kopieren von Daten in ein und aus einem Dateisystem finden Sie im Abschnitt [JSON-Beispiele](#json-examples-for-copying-data-to-and-from-file-system) in diesem Artikel.

Die folgenden Abschnitte enthalten Details zu JSON-Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für Dateisysteme verwendet werden:

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts
Sie können ein lokales Dateisystem mithilfe eines verknüpften Diensts vom Typ **lokaler Dateiserver** mit einer Azure Data Factory verknüpfen. Die folgende Tabelle enthält Beschreibungen der JSON-Elemente, die für den mit dem lokalen Dateiserver verknüpften Dienst spezifisch sind.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| type |Stellen Sie sicher, dass die Eigenschaft „type“ auf **OnPremisesFileServer** festgelegt ist. |Ja |
| host |Gibt den Stammpfad des Ordners an, den Sie kopieren möchten. Verwenden Sie für Sonderzeichen in der Zeichenfolge das Escapezeichen „\“. Beispiele finden Sie unter [Beispieldefinitionen für verknüpfte Dienste und Datasets](#sample-linked-service-and-dataset-definitions) . |Ja |
| userid |Geben Sie die ID des Benutzers an, der auf dem Server zugreifen darf. |Nein (wenn Sie "encryptedCredential" auswählen) |
| password |Geben Sie das Kennwort für das Benutzerkonto (userid) an. |Nein (wenn Sie "encryptedCredential" auswählen) |
| encryptedCredential |Geben Sie die verschlüsselten Anmeldeinformationen an. Diese können Sie durch Ausführen des Cmdlets „New-AzDataFactoryEncryptValue“ abrufen. |Nein (wenn Sie "userid" und "password" unverschlüsselt angeben) |
| gatewayName |Gibt den Name des Gateways an, das der Data Factory-Dienst zum Verbinden mit dem lokalen Dateiserver verwenden soll. |Ja |


### <a name="sample-linked-service-and-dataset-definitions"></a>Beispieldefinitionen für verknüpfte Dienste und Datasets
| Szenario | Host in der Definition des verknüpften Diensts | folderPath in der Datasetdefinition |
| --- | --- | --- |
| Lokaler Ordner auf dem Datenverwaltungsgateway-Computer:  <br/><br/>Beispiele: D:\\\* oder D:\Ordner\Unterordner\\* |D:\\\\ (für Datenverwaltungsgateway 2.0 und neuere Versionen) <br/><br/> localhost (für ältere Versionen als Datenverwaltungsgateway 2.0) |.\\\\ oder Ordner\\\\Unterordner (für Datenverwaltungsgateway 2.0 und neuere Versionen) <br/><br/>D:\\\\ oder D:\\\\Ordner\\\\Unterordner (für Gatewayversionen unter 2.0) |
| Freigegebener Remoteordner: <br/><br/>Beispiele: \\\\MeinServer\\Freigabe\\\* oder \\\\MeinServer\\Freigabe\\Ordner\\Unterordner\\* |\\\\\\\\MeinServer\\\\Freigabe |.\\\\ oder Ordner\\\\Unterordner |

>[!NOTE]
>Bei der Erstellung auf der Benutzeroberfläche müssen Sie keinen doppelten Rückwärtsschrägstrich (`\\`) wie bei JSON als Escapezeichen eingeben. Geben Sie einen einfachen Rückwärtsschrägstrich an.

### <a name="example-using-username-and-password-in-plain-text"></a>Beispiel: Verwenden von „username“ und „password“ im Nur-Text-Format

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

### <a name="example-using-encryptedcredential"></a>Beispiel: Verwenden von encryptedcredential

```JSON
{
  "Name": " OnPremisesFileServerLinkedService ",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "D:\\",
      "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
      "gatewayName": "mygateway"
    }
  }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften
Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie unter [Erstellen von Datasets](data-factory-create-datasets.md). Abschnitte wie „structure“, „availability“ und „policy“ des JSON-Codes eines Datasets sind bei allen Dataset-Typen ähnlich.

Der Abschnitt „typeproperties“ ist bei jeder Art von Dataset unterschiedlich. Es enthält Informationen wie den Speicherort und das Format der Daten im Datenspeicher. Der Abschnitt typeProperties für ein Dataset des Typs **FileShare** hat die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| folderPath |Gibt den Unterpfad zum Ordner an. Verwenden Sie für Sonderzeichen in der Zeichenfolge das Escapezeichen '\'. Der Platzhalterfilter wird nicht unterstützt. Beispiele finden Sie unter [Beispieldefinitionen für verknüpfte Dienste und Datasets](#sample-linked-service-and-dataset-definitions) .<br/><br/>Sie können diese Eigenschaft mit **partitionBy** kombinieren, um Ordnerpfade auf der Grundlage von Datum und Uhrzeit für Start und Ende des Slices zu erhalten. |Ja |
| fileName |Geben Sie den Namen der Datei in **folderPath** an, wenn die Tabelle auf eine bestimmte Datei im Ordner verweisen soll. Wenn Sie keine Werte für diese Eigenschaft angeben, verweist die Tabelle auf alle Dateien im Ordner.<br/><br/>Wenn **fileName** nicht für ein Ausgabedataset und **preserveHierarchy** nicht in der Aktivitätssenke angegeben ist, hat der Name der generierten Datei das folgende Format: <br/><br/>`Data.<Guid>.txt` (Beispiel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt). |Nein  |
| fileFilter |Geben Sie einen Filter zur Auswahl einer Teilmenge der Dateien in "folderPath" statt alle Dateien an. <br/><br/>Zulässige Werte sind: `*` (mehrere Zeichen) und `?` (einzelnes Zeichen).<br/><br/>Beispiel 1: „fileFilter“: „*.log“<br/>Beispiel 2: „fileFilter“: „2014-1-?.txt“<br/><br/>Beachten Sie, dass sich „fileFilter“ für das Eingabedataset „FileShare“ eignet. |Nein  |
| partitionedBy |Sie können mit „partitionedBy“ für Zeitreihendaten einen dynamischen Wert für „folderPath“ und „filename“ angeben. Beispiel: Parametrisierung von „folderPath“ für Daten nach Stunde. |Nein  |
| format | Die folgenden Formattypen werden unterstützt: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** und **ParquetFormat**. Sie müssen die **type** -Eigenschaft unter „format“ auf einen dieser Werte festlegen. Weitere Informationen finden Sie in den Abschnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-Format](data-factory-supported-file-and-compression-formats.md#orc-format) und [Parquet-Format](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Wenn Sie **Dateien unverändert zwischen dateibasierten Speichern kopieren** möchten (binäre Kopie), können Sie den Formatabschnitt bei den Definitionen von Eingabe- und Ausgabedatasets überspringen. |Nein  |
| compression | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Folgende Typen werden unterstützt: **GZip**, **Deflate**, **BZip2** und **ZipDeflate**. Folgende Ebenen werden unterstützt: **Optimal** und **Fastest**. Weitere Informationen finden Sie unter [Datei- und Komprimierungsformate in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nein  |

> [!NOTE]
> „fileName“ und „fileFilter“ können nicht gleichzeitig verwendet werden.

### <a name="using-partitionedby-property"></a>Nutzen der partitionedBy-Eigenschaft
Wie im vorherigen Abschnitt erwähnt, können die **partitionedBy**-Eigenschaft, [Data Factory-Funktionen und Systemvariablen](data-factory-functions-variables.md) verwendet werden, um für Zeitreihendaten einen dynamischen Wert für folderPath und filename anzugeben.

Weitere Informationen zu Zeitreihen-Datasets, Planung und Slices finden Sie unter [Erstellen von Datasets](data-factory-create-datasets.md), [Planung und Ausführung](data-factory-scheduling-and-execution.md) und [Erstellen von Pipelines](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Beispiel 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Im obigen Beispiel wird {Slice} durch den Wert der Data Factory-Systemvariablen SliceStart im Format (JJJJMMTTHH) ersetzt. „SliceStart“ bezieht sich auf die Startzeit des Slices. "folderPath" unterscheidet sich für jeden Slice. Beispiel: "wikidatagateway/wikisampledataout/2014100103" oder "wikidatagateway/wikisampledataout/2014100104".

#### <a name="sample-2"></a>Beispiel 2:

```JSON
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
Eine vollständige Liste der Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabedatasets und Richtlinien sind für alle Arten von Aktivitäten verfügbar. Eigenschaften im Abschnitt **typeProperties** der Aktivität können dagegen je nach Aktivitätstyp variieren.

Für die Kopieraktivität variieren die Eigenschaften je nach Art der Quellen und Senken. Wenn Sie Daten aus einem lokalen Dateisystem verschieben, legen Sie den Quelltyp in der Kopieraktivität auf **FileSystemSource** fest. Analog dazu legen Sie beim Verschieben von Daten in ein lokales Dateisystem den Senkentyp in der Kopieraktivität auf **FileSystemSink** fest. Dieser Abschnitt enthält eine Liste der von FileSystemSource und FileSystemSink unterstützten Eigenschaften.

**FileSystemSource** unterstützt die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| recursive |Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. |True/False (Standardwert) |Nein  |

**FileSystemSink** unterstützt die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| copyBehavior |Definiert das Verhalten beim Kopieren, wenn die Quelle "BlobSource" oder "FileSystem" ist. |**PreserveHierarchy:** Behält die Dateihierarchie im Zielordner bei. Der relative Pfad der Quelldatei zum Quellordner entspricht dem relativen Pfad der Zieldatei zum Zielordner.<br/><br/>**FlattenHierarchy:** Alle Dateien aus dem Quellordner werden auf der ersten Ebene des Zielordners erstellt. Die Namen der Zieldateien werden automatisch generiert.<br/><br/>**MergeFiles:** Alle Dateien aus dem Quellordner werden in einer Datei zusammengeführt. Wenn der Datei-/Blob-Name angegeben wurde, entspricht der Name dem angegebenen Namen, andernfalls dem automatisch generierten Dateinamen. |Nein  |

### <a name="recursive-and-copybehavior-examples"></a>Beispiele für "recursive" und "copyBehavior"
Dieser Abschnitt beschreibt das resultierende Verhalten des Kopiervorgangs für verschiedene Kombinationen von Werten für recursive- und copyBehavior-Eigenschaften.

| recursive-Wert | copyBehavior-Wert | Resultierendes Verhalten |
| --- | --- | --- |
| true |preserveHierarchy |Für den Quellordner „Ordner1“ mit der folgenden Struktur:<br/><br/>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei5<br/><br/>wird der Zielordner „Ordner1“ mit der gleichen Struktur erstellt wie die Quelle:<br/><br/>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei5 |
| true |flattenHierarchy |Für den Quellordner „Ordner1“ mit der folgenden Struktur:<br/><br/>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei5<br/><br/>wird der Zielordner „Ordner1“ mit der folgenden Struktur erstellt: <br/><br/>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei5 |
| true |mergeFiles |Für den Quellordner „Ordner1“ mit der folgenden Struktur:<br/><br/>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei5<br/><br/>wird der Zielordner „Ordner1“ mit der folgenden Struktur erstellt: <br/><br/>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp; Inhalte von Datei1 + Datei2 + Datei3 + Datei4 + Datei5 werden in einer Datei mit einem automatisch generierten Namen zusammengeführt. |
| false |preserveHierarchy |Für den Quellordner „Ordner1“ mit der folgenden Struktur:<br/><br/>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei5<br/><br/>wird der Zielordner „Ordner1“ mit der folgenden Struktur erstellt:<br/><br/>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/><br/>Unterordner1 mit Datei3, Datei4 und Datei5 wird nicht übernommen. |
| false |flattenHierarchy |Für den Quellordner „Ordner1“ mit der folgenden Struktur:<br/><br/>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei5<br/><br/>wird der Zielordner „Ordner1“ mit der folgenden Struktur erstellt:<br/><br/>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei2<br/><br/>Unterordner1 mit Datei3, Datei4 und Datei5 wird nicht übernommen. |
| false |mergeFiles |Für den Quellordner „Ordner1“ mit der folgenden Struktur:<br/><br/>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei5<br/><br/>wird der Zielordner „Ordner1“ mit der folgenden Struktur erstellt:<br/><br/>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Inhalte von Datei1 + Datei2 werden zu einer Datei mit einem automatisch generierten Namen zusammengeführt.<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei1<br/><br/>Unterordner1 mit Datei3, Datei4 und Datei5 wird nicht übernommen. |

## <a name="supported-file-and-compression-formats"></a>Unterstützte Datei- und Komprimierungsformate
Einzelheiten finden Sie im Artikel [Datei- und Komprimierungsformate in Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>JSON-Beispiele zum Kopieren von Daten in das bzw. aus dem Dateisystem
Die folgenden Beispiele zeigen JSON-Beispieldefinitionen, die Sie zum Erstellen einer Pipeline mit dem [Azure-Portal](data-factory-copy-activity-tutorial-using-azure-portal.md), mit [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) oder [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) verwenden können. Sie zeigen, wie Sie Daten in und aus einem lokalen Dateisystem und Azure Blob Storage kopieren. Allerdings können Sie mit der Kopier-Aktivität in Azure Data Factory.Daten *direkt* aus den Quellen in alle unter [Unterstützte Datenquellen und Senken](data-factory-data-movement-activities.md#supported-data-stores-and-formats) aufgeführten Senken kopieren.

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Beispiel: Kopieren von Daten aus einem lokalen Dateisystem in Azure Blob Storage
In diesem Beispiel wird gezeigt, wie Sie Daten aus einem lokalen Dateisystem in Azure Blob Storage kopieren. Das Beispiel enthält die folgenden Data Factory-Entitäten:

* Einen verknüpften Dienst des Typs [OnPremisesFileServer](#linked-service-properties)
* Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [FileShare](#dataset-properties)
* Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [FileSystemSource](#copy-activity-properties) und [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) verwendet

Im folgenden Beispiel werden Zeitreihendaten aus dem lokalen Dateisystem stündlich in ein Azure Blob Storage kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten nach den Beispielen beschrieben.

Richten Sie zuerst das Datenverwaltungsgateway wie unter [Verschieben von Daten zwischen lokalen Quellen und der Cloud mit dem Datenverwaltungsgateway](data-factory-move-data-between-onprem-and-cloud.md) beschrieben ein.

**Mit lokalem Dateiserver verknüpfter Dienst:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Es empfiehlt sich, anstelle der Eigenschaften **userid** und **password** die Eigenschaft **encryptedCredential** zu verwenden. Weitere Informationen zu diesem verknüpften Dienst finden Sie unter [Mit lokalem Dateiserver verknüpfter Dienst](#linked-service-properties).

**Mit Azure Storage verknüpfter Dienst:**

```JSON
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

**Eingabedataset aus dem lokalem Dateisystem:**

Daten werden stündlich aus einer neuen Datei abgerufen. Die Eigenschaften „folderPath“ und „fileName“ werden auf der Grundlage der Slice-Startzeit bestimmt.

Die Festlegung von `"external": "true"` teilt dem Data Factory-Dienst mit, dass das Dataset für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

```JSON
{
  "name": "OnpremisesFileSystemInput",
  "properties": {
    "type": " FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
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

**Azure Blob Storage-Ausgabedataset:**

Daten werden stündlich in ein neues Blob geschrieben (frequency: hour, interval: 1). Der Ordnerpfad des Blobs wird basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Im Ordnerpfad werden Jahr, Monat, Tag und die Stundenteile der Startzeit verwendet.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
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

**Kopieraktivität in einer Pipeline mit einer Dateisystemquelle und einer Blobsenke:**

Die Pipeline enthält eine Kopieraktivität, die für die Verwendung der Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **FileSystemSource** und der Typ **sink** auf **BlobSink** festgelegt.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T19:00:00",
    "description":"Pipeline for copy activity",
    "activities":[
      {
        "name": "OnpremisesFileSystemtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "OnpremisesFileSystemInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "FileSystemSource"
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

### <a name="example-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>Beispiel: Kopieren von Daten aus Azure SQL-Datenbank in ein lokales Dateisystem
Dieses Beispiel zeigt Folgendes:

* Einen verknüpften Dienst des Typs [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties)
* Einen verknüpften Dienst des Typs [OnPremisesFileServer](#linked-service-properties)
* Ein Eingabedataset des Typs [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
* Ein Ausgabedataset des Typs [FileShare](#dataset-properties).
* Eine Pipeline mit Kopieraktivität, die [SqlSource](data-factory-azure-sql-connector.md##copy-activity-properties) und [FileSystemSink](#copy-activity-properties) verwendet.

Im Beispiel werden Zeitreihendaten aus einer Azure SQL-Tabelle stündlich in ein lokales Dateisystem kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten nach den Beispielen beschrieben.

**Mit Azure SQL-Datenbank verknüpfter Dienst:**

```JSON
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

**Mit lokalem Dateiserver verknüpfter Dienst:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Es empfiehlt sich, anstelle der Eigenschaften **userid** und **password** die Eigenschaft **encryptedCredential** zu verwenden. Weitere Informationen zu diesem verknüpften Dienst finden Sie unter [Eigenschaften des verknüpften Diensts „OnPremisesFileServer“](#linked-service-properties) .

**Azure SQL-Eingabedataset:**

Im Beispiel wird vorausgesetzt, dass Sie die Tabelle „MyTable“ in Azure SQL erstellt haben, die für Zeitreihendaten eine Spalte namens „timestampcolumn“ enthält.

Die Festlegung von ``“external”: ”true”`` teilt dem Data Factory-Dienst mit, dass das Dataset für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

```JSON
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

**Ausgabedataset aus dem lokalem Dateisystem:**

Daten werden stündlich in eine neue Datei kopiert. Ordnerpfad (folderPath) und Dateiname (fileName) für das Blob werden auf der Grundlage der Slice-Startzeit bestimmt.

```JSON
{
  "name": "OnpremisesFileSystemOutput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
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

**Kopieraktivität in einer Pipeline mit einer SQL-Quelle und einer Dateisystemsenke:**

Die Pipeline enthält eine Kopieraktivität, die für die Verwendung der Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **SqlSource** und der Typ **sink** auf **FileSystemSink** festgelegt. Die für die **SqlReaderQuery** -Eigenschaft angegebene SQL-Abfrage wählt die Daten der letzten Stunde zum Kopieren aus.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T20:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLtoOnPremisesFile",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "OnpremisesFileSystemOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "FileSystemSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 3,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

Sie können in der Definition der Kopieraktivität auch Spalten aus dem Quelldataset Spalten im Senkendataset zuordnen. Weitere Informationen finden Sie unter [Zuordnen von Datasetspalten in Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Leistung und Optimierung
 Im [Handbuch zur Leistung und Optimierung der Kopieraktivität](data-factory-copy-activity-performance.md) werden wichtige Faktoren beschrieben, die sich auf die Leistung der Datenverschiebung (Kopieraktivität) in Azure Data Factory auswirken, sowie verschiedene Möglichkeiten zur Leistungsoptimierung.
