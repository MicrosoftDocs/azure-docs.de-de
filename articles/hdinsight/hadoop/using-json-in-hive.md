---
title: Analysieren und Verarbeiten von JSON mit Apache Hive – Azure HDInsight
description: Informationen zum Verwenden und Analysieren von JSON-Dokumenten mithilfe von Apache Hive in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 9a7d3992ecd2c74947eaa1071b97b2032000c749
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547604"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Analysieren und Verarbeiten von JSON-Dokumenten mithilfe von Apache Hive in Azure HDInsight

Erfahren Sie mehr über das Verarbeiten und Analysieren von JSON-Dateien (JavaScript Object Notation) mithilfe von Apache Hive in Azure HDInsight. In diesem Artikel wird das folgende JSON-Dokument verwendet:

```json
{
  "StudentId": "trgfg-5454-fdfdg-4346",
  "Grade": 7,
  "StudentDetails": [
    {
      "FirstName": "Peggy",
      "LastName": "Williams",
      "YearJoined": 2012
    }
  ],
  "StudentClassCollection": [
    {
      "ClassId": "89084343",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "High",
      "Score": 93,
      "PerformedActivity": false
    },
    {
      "ClassId": "78547522",
      "ClassParticipation": "NotSatisfied",
      "ClassParticipationRank": "None",
      "Score": 74,
      "PerformedActivity": false
    },
    {
      "ClassId": "78675563",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "Low",
      "Score": 83,
      "PerformedActivity": true
    }
  ]
}
```

Die Datei finden Sie unter `wasb://processjson@hditutorialdata.blob.core.windows.net/`. Weitere Informationen zur Verwendung von Azure Blob Storage mit HDInsight finden Sie unter [Verwenden von HDFS-kompatiblem Azure Blob Storage mit Apache Hadoop in HDInsight](../hdinsight-hadoop-use-blob-storage.md). Sie können die Datei in den Standardcontainer des Clusters kopieren.

In diesem Artikel verwenden Sie die Apache Hive-Konsole. Anweisungen zum Öffnen der Hive-Konsole finden Sie unter [Verwenden der Apache Ambari Hive-Ansicht mit Apache Hadoop in HDInsight](apache-hadoop-use-hive-ambari-view.md).

> [!NOTE]  
> Hive View ist in HDInsight 4.0 nicht mehr verfügbar.

## <a name="flatten-json-documents"></a>Vereinfachen von JSON-Dokumenten

Die im nächsten Abschnitt aufgeführten Methoden erfordern, dass das JSON-Dokument aus einer einzelnen Zeile besteht. Sie müssen also das JSON-Dokument zu einer Zeichenfolge vereinfachen. Wenn Ihr JSON-Dokument bereits vereinfacht wurde, können Sie diesen Schritt überspringen und direkt mit dem nächsten Abschnitt fortfahren und die JSON-Daten analysieren. Führen Sie zum Vereinfachen des JSON-Dokuments folgendes Skript aus:

```sql
DROP TABLE IF EXISTS StudentsRaw;
CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

DROP TABLE IF EXISTS StudentsOneLine;
CREATE EXTERNAL TABLE StudentsOneLine
(
  json_body string
)
STORED AS TEXTFILE LOCATION '/json/students';

INSERT OVERWRITE TABLE StudentsOneLine
SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
      GROUP BY INPUT__FILE__NAME;

SELECT * FROM StudentsOneLine
```

Die unformatierte JSON-Datei befindet sich unter `wasb://processjson@hditutorialdata.blob.core.windows.net/`. Die Hive-Tabelle **StudentsRaw** verweist auf das unformatierte, nicht vereinfachte JSON-Dokument.

Die Hive-Tabelle **StudentsOneLine** speichert die Daten im HDInsight-Standarddateisystem unter dem Pfad **/json/students/** .

Die **INSERT** -Anweisung füllt die Tabelle **StudentOneLine** mit den vereinfachten JSON-Daten.

Die **SELECT** -Anweisung gibt nur eine Zeile zurück.

Dies ist die Ausgabe der **SELECT** -Anweisung:

![Vereinfachen des JSON-Dokuments mit HDInsight](./media/using-json-in-hive/hdinsight-flatten-json.png)

## <a name="analyze-json-documents-in-hive"></a>Analysieren von JSON-Dokumenten in Hive

Hive stellt drei verschiedene Mechanismen zum Ausführen von Abfragen bei JSON-Dokumenten bereit, Sie können jedoch auch Ihren eigenen schreiben:

* Verwenden Sie die benutzerdefinierte Funktion (UDF) GET_JSON_OBJECT.
* Verwenden Sie die JSON_TUPLE-UDF.
* Verwenden Sie ein benutzerdefiniertes Serialisierungs-/Deserialisierungsprogramm (SerDe).
* Schreiben Sie mit Python oder anderen Sprachen eine eigene UDF. Weitere Informationen zum Ausführen Ihres eigenen Python-Codes mit Hive finden Sie unter [Python-UDF mit Apache Hive und Apache Pig](./python-udf-hdinsight.md).

### <a name="use-the-get_json_object-udf"></a>Verwenden der GET_JSON_OBJECT-UDF

In Hive ist eine integrierte UDF mit dem Namen [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) verfügbar, die JSON-Abfragen während der Laufzeit ausführt. Diese Methode akzeptiert zwei Argumente: den Tabellennamen und den Methodennamen. Der Methodenname enthält das vereinfachte JSON-Dokument und das JSON-Feld, das analysiert werden muss. An einem Beispiel soll verdeutlicht werden, wie diese UDF funktioniert.

Die folgende Abfrage gibt den Vor- und Nachnamen aller Studierenden zurück:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Dies ist die Ausgabe, wenn diese Abfrage im Konsolenfenster ausgeführt wurde:

![Apache Hive gets json object UDF](./media/using-json-in-hive/hdinsight-get-json-object.png)

Für die get-json_object-UDF gibt es Einschränkungen:

* Da für jedes Feld in der Abfrage eine erneute Analyse der Abfrage erforderlich ist, wird die Leistung beeinträchtigt.
* \_GET **JSON_OBJECT()** gibt die Zeichenfolgendarstellung eines Arrays zurück. Zum Umwandeln dieses Arrays in ein Hive-Array müssen Sie reguläre Ausdrücke verwenden, um die eckigen Klammern „[“ und „]“ zu ersetzen, und Sie müssen die Funktion „Call Split“ verwenden, um das Array zu erhalten.

Aufgrund dieser Umwandlung wird im Hive-Wiki die Verwendung von **json_tuple** empfohlen.  

### <a name="use-the-json_tuple-udf"></a>Verwenden der JSON_TUPLE-UDF

Eine weitere in Hive verfügbare UDF ist [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple). Diese Funktion ist besser als [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Sie akzeptiert einen Satz von Schlüsseln und eine JSON-Zeichenfolge. Anschließend gibt sie ein Tupel von Werten zurück. Die folgende Abfrage gibt die ID der Studierenden und die Klasse aus dem JSON-Dokument zurück:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Die Ausgabe des Skripts in der Hive-Konsole:

![Apache Hive json query results](./media/using-json-in-hive/hdinsight-json-tuple.png)

Die `json_tuple`-UDF verwendet die Syntax [Lateral View](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) in Hive, wodurch „json\_tuple“ eine virtuelle Tabelle erstellen kann, indem die UDT-Funktion auf jede Zeile der Originaltabelle angewendet wird. Komplexe JSONs werden durch die wiederholte Verwendung von **LATERAL VIEW** zu unhandlich. Darüber hinaus kann **JSON_TUPLE** keine geschachtelten JSONs verarbeiten.

### <a name="use-a-custom-serde"></a>Verwenden eines benutzerdefinierten Serialisierungs-/Deserialisierungsprogramms (SerDe)

SerDe ist die beste Wahl für das Analysieren von geschachtelten JSON-Dokumenten. Sie können damit das JSON-Schema definieren und dieses dann zum Analysieren des Dokuments verwenden. Anweisungen hierzu finden Sie unter [How to use a Custom JSON Serde with Microsoft Azure HDInsight (Verwenden eines benutzerdefinierten JSON-SerDe mit Microsoft Azure HDInsight)](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Zusammenfassung

Der Typ des JSON-Operators in Hive, den Sie auswählen, hängt vom Szenario ab. Wählen Sie für die Suche nach einem einzigen Feld in einem einfachen JSON-Dokument die Hive-UDF **get_json_object** aus. Wenn mehrere Suchschlüssel vorliegen, können Sie die UDF **json_tuple** verwenden. Verwenden Sie für geschachtelte Dokumente das **JSON-SerDe** .

## <a name="next-steps"></a>Nächste Schritte

Verwandte Artikel finden Sie unter:

* [Verwenden von Apache Hive und HiveQL mit Apache Hadoop in HDInsight zum Analysieren einer Apache Log4j-Beispieldatei](./hdinsight-use-hive.md)
* [Tutorial: Extrahieren, Transformieren und Laden von Daten mithilfe von Interactive Query in Azure HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)