---
title: Azure Stream Analytics-Diagnoseprotokolldatenfehler
description: In diesem Artikel werden die verschiedenen Fehler bei der Eingabe und Ausgabe von Daten erklärt, die bei der Verwendung von Azure Stream Analytics auftreten können.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: b00eb12092838746f4bfe16f00eac55df9224b09
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65607109"
---
# <a name="azure-stream-analytics-data-errors"></a>Datenfehler in Azure Stream Analytics

Wenn es eine Diskrepanz zwischen den Daten gibt, die von einem Azure Stream Analytics-Auftrag verarbeitet werden, sendet Stream Analytics ein Datenfehlerereignis an die Diagnoseprotokolle. Stream Analytics schreibt detaillierte Informationen und Beispielereignisse in die Diagnoseprotokolle, wenn Datenfehler auftreten. Bei manchen Fehlern wird auch eine Zusammenfassung dieser Informationen über Benachrichtigungen im Portal bereitgestellt.

In diesem Artikel erhalten Sie einen Überblick über die verschiedenen Fehlertypen, Gründe und Details zum Diagnoseprotokoll für Fehler bei der Eingabe oder Ausgabe von Daten.

## <a name="diagnostic-log-schema"></a>Diagnoseprotokollschema

Weitere Informationen zum Schema für Diagnoseprotokolle finden Sie unter [Problembehandlung bei Azure Stream Analytics mit Diagnoseprotokollen](stream-analytics-job-diagnostic-logs.md#diagnostics-logs-schema). Im folgenden JSON-Codeausschnitt finden Sie einen Beispielwert für das **Eigenschaften**-Feld eines Diagnoseprotokolls für einen Datenfehler.

```json
{
    "Source": "InputTelemetryData",
    "Type": "DataError",
    "DataErrorType": "InputDeserializerError.InvalidData",
    "BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "Message": "Input Message Id: https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt Error: Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "ExampleEvents": "[\"1,2\\\\u000d\\\\u000a3,4\\\\u000d\\\\u000a5,6\"]",
    "FromTimestamp": "2019-03-22T22:34:18.5664937Z",
    "ToTimestamp": "2019-03-22T22:34:18.5965248Z",
    "EventCount": 1
}
```

## <a name="input-data-errors"></a>Fehler bei der Eingabe von Daten

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* Ursache: Der ausgewählte Eingabekomprimierungstyp stimmt nicht mit den Daten überein.
* Benachrichtigung im Portal: Ja
* Art des Diagnoseprotokolls: Warnung
* Protokolldetails
   * Eingabenachrichtbezeichner. Bei einem Event Hub ist der Bezeichner „PartitionId“, „Offset“ und „Sequenznummer“.

**Fehlermeldung**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* Ursache: Der Header der Eingabedaten ist ungültig. Ein Beispiel: Eine CSV-Datei weist Spalten mit doppelten Namen auf.
* Benachrichtigung im Portal: Ja
* Art des Diagnoseprotokolls: Warnung
* Protokolldetails
   * Eingabenachrichtbezeichner. 
   * Tatsächliche Nutzlast bis auf einige Kilobytes genau.

**Fehlermeldung**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* Ursache: Die über CREATE TABLE oder TIMESTAMP definierten Eingabespalten sind nicht vorhanden.
* Benachrichtigung im Portal: Ja
* Art des Diagnoseprotokolls: Warnung
* Protokolldetails
   * Eingabenachrichtbezeichner. 
   * Name der Spalten, die fehlen. 
   * Tatsächliche Nutzlast bis auf einige Kilobytes genau.

**Fehlermeldungen**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* Ursache: Die Eingabe konnte nicht in den Typ konvertiert werden, der in der CREATE TABLE-Anweisung angegeben ist.
* Benachrichtigung im Portal: Ja
* Art des Diagnoseprotokolls: Warnung
* Protokolldetails
   * Eingabenachrichtbezeichner. 
   * Name der Spalte und erwarteter Typ.

**Fehlermeldungen**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* Ursache: Die Eingabedaten weisen nicht das richtige Format auf. Ein Beispiel: Die Eingabe ist in keinem gültigen JSON-Format.
* Benachrichtigung im Portal: Ja
* Art des Diagnoseprotokolls: Warnung
* Protokolldetails
   * Eingabenachrichtbezeichner. 
   * Tatsächliche Nutzlast bis auf einige Kilobytes genau.

**Fehlermeldungen**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* Ursache: Der Wert des TIMESTAMP BY-Ausdrucks kann nicht in datetime konvertiert werden.
* Benachrichtigung im Portal: Ja
* Art des Diagnoseprotokolls: Warnung
* Protokolldetails
   * Eingabenachrichtbezeichner. 
   * Fehlermeldung. 
   * Tatsächliche Nutzlast bis auf einige Kilobytes genau.

**Fehlermeldung**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* Ursache: Der Wert der Zeitstempelspalte TIMESTAMP BY OVER ist NULL.
* Benachrichtigung im Portal: Ja
* Art des Diagnoseprotokolls: Warnung
* Protokolldetails
   * Tatsächliche Nutzlast bis auf einige Kilobytes genau.

**Fehlermeldung**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* Ursache: Der Unterschied zwischen der Anwendungszeit und der Eingangszeit ist größer als das Toleranzfenster für Eingangsverzögerung.
* Benachrichtigung im Portal: Nein
* Art des Diagnoseprotokolls: Information
* Protokolldetails
   * Anwendungszeit und Eingangszeit 
   * Tatsächliche Nutzlast bis auf einige Kilobytes genau.

**Fehlermeldung**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* Ursache: Der Unterschied zwischen der Anwendungszeit und der Eingangszeit beträgt mehr als fünf Minuten.
* Benachrichtigung im Portal: Nein
* Art des Diagnoseprotokolls: Information
* Protokolldetails
   * Anwendungszeit und Eingangszeit 
   * Tatsächliche Nutzlast bis auf einige Kilobytes genau.

**Fehlermeldung**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* Ursache: Das Ereignis wird entsprechend des definierten Toleranzfensters für Fehlordnung als fehlgeordnet angesehen.
* Benachrichtigung im Portal: Nein
* Art des Diagnoseprotokolls: Information
* Protokolldetails
   * Tatsächliche Nutzlast bis auf einige Kilobytes genau.

**Fehlermeldung**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Fehler bei der Ausgabe von Daten

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* Ursache: Die für die Ausgabe erforderliche Spalte ist nicht vorhanden. Ein Beispiel: Eine Spalte, die als PartitionKey für eine Azure-Tabelle definiert wurde, ist nicht vorhanden.
* Benachrichtigung im Portal: Ja
* Art des Diagnoseprotokolls: Warnung
* Protokolldetails
   * Name der Spalte und entweder der Zeilenbezeichner oder der Teil der Zeile.

**Fehlermeldung**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* Ursache: Der Spaltenwert entspricht nicht der Ausgabe. Ein Beispiel: Der Spaltenname ist keine gültige Azure-Tabellenspalte.
* Benachrichtigung im Portal: Ja
* Art des Diagnoseprotokolls: Warnung
* Protokolldetails
   * Name der Spalte und entweder der Zeilenbezeichner oder der Teil der Zeile.

**Fehlermeldung**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* Ursache: Eine Spalte kann in der Ausgabe nicht in einen gültigen Typ konvertiert werden. Ein Beispiel: Der Wert einer Spalte ist nicht kompatibel mit Einschränkungen oder Typen, die in der SQL-Tabelle definiert wurden.
* Benachrichtigung im Portal: Ja
* Art des Diagnoseprotokolls: Warnung
* Protokolldetails
   * Name der Spalte.
   * Entweder der Zeilenbezeichner oder der Teil der Zeile.

**Fehlermeldung**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* Ursache: Der Wert der Nachricht ist größer als die unterstützte Ausgabegröße. Ein Beispiel: Eine Zeile für eine Event Hub-Ausgabe ist größer als 1 MB.
* Benachrichtigung im Portal: Ja
* Art des Diagnoseprotokolls: Warnung
* Protokolldetails
   * Entweder der Zeilenbezeichner oder der Teil der Zeile.

**Fehlermeldung**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* Ursache: Eine Zeile enthält bereits eine Spalte mit demselben Namen wie eine Systemspalte. Ein Beispiel: Eine CosmosDB-Ausgabe mit einer Spalte namens ID, obwohl dieser Spaltenname bereits für eine andere Spalte verwendet wird.
* Benachrichtigung im Portal: Ja
* Art des Diagnoseprotokolls: Warnung
* Protokolldetails
   * Name der Spalte.
   * Entweder der Zeilenbezeichner oder der Teil der Zeile.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>Nächste Schritte

* [Problembehandlung bei Azure Stream Analytics mit Diagnoseprotokollen](stream-analytics-job-diagnostic-logs.md)

* [Grundlegendes zur Stream Analytics-Auftragsüberwachung und zum Überwachen von Abfragen](stream-analytics-monitoring.md)
