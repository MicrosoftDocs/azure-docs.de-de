---
title: Leistung von Phoenix in Azure HDInsight
description: Hier finden Sie bewährte Methoden zur Optimierung der Leistung von Phoenix.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 4fc4d1843ddb8d007ca062d928ebbddf90909583
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64690033"
---
# <a name="apache-phoenix-performance-best-practices"></a>Bewährte Methoden für Leistung – Apache Phoenix

Der wichtigste Leistungsaspekt von [Apache Phoenix](https://phoenix.apache.org/) ist die Optimierung der zugrunde liegenden [Apache HBase](https://hbase.apache.org/)-Lösung. Phoenix erstellt auf der Grundlage von HBase ein relationales Datenmodell, das SQL-Abfragen in HBase-Vorgänge (beispielsweise Scans) konvertiert. Die Leistung von Phoenix wird durch das Design Ihres Tabellenschemas, die gewählten Felder in Ihrem Primärschlüssel und deren Reihenfolge sowie durch den Einsatz von Indizes beeinflusst.

## <a name="table-schema-design"></a>Design des Tabellenschemas

Wenn Sie in Phoenix eine Tabelle erstellen, wird diese in einer HBase-Tabelle gespeichert. Die HBase-Tabelle enthält Gruppen von Spalten (Spaltenfamilien), auf die gemeinsam zugegriffen wird. Eine Zeile in der Phoenix-Tabelle entspricht einer Zeile in der HBase-Tabelle. Dabei besteht jede Zeile aus Zellen mit Versionsangabe, die mindestens einer Spalte zugeordnet sind. Logisch betrachtet handelt es sich bei einer einzelnen HBase-Zeile um eine Sammlung von Schlüssel-Wert-Paaren, die jeweils den gleichen rowkey-Wert besitzen. Jedes Schlüssel-Wert-Paar besitzt also ein rowkey-Attribut, und der Wert dieses rowkey-Attributs ist für eine bestimmte Zeile identisch.

Das Schemadesign einer Phoenix-Tabelle umfasst das Design des Primärschlüssels, der Spaltenfamilie und der Einzelspalten sowie die Partitionierung der Daten.

### <a name="primary-key-design"></a>Design des Primärschlüssels

Der für eine Tabelle in Phoenix definierte Primärschlüssel bestimmt, wie Daten innerhalb des Zeilenschlüssels (rowkey) der zugrunde liegenden HBase-Tabelle gespeichert werden. In HBase kann ausschließlich mit dem Zeilenschlüssel auf eine bestimmte Zeile zugegriffen werden. Darüber hinaus werden in einer HBase-Tabelle gespeicherte Daten nach dem Zeilenschlüssel sortiert. Zur Erstellung des rowkey-Werts verkettet Phoenix die Werte der einzelnen Spalten in der Zeile in der im Primärschlüssel definierten Reihenfolge.

Als Beispiel soll eine Kontakttabelle mit Angaben zu Vorname, Nachname, Telefonnummer und Adresse dienen, die sich alle in der gleichen Spaltenfamilie befinden. Sie können beispielsweise einen Primärschlüssel auf der Grundlage einer ansteigenden Sequenznummer definieren:

|rowkey|       address|   phone| firstName| lastName|
|------|--------------------|--------------|-------------|--------------|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole|
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji|

Wenn Sie allerdings häufig Abfragen auf der Grundlage des Nachnamens (lastName) durchführen, erzielen Sie mit diesem Primärschlüssel möglicherweise keine optimale Leistung, da für jede Abfrage ein vollständiger Tabellenscan erforderlich ist, um den Wert jedes Nachnamens zu lesen. Sie können stattdessen einen Primärschlüssel definieren, der auf den Spalten für Nachname (lastName), Vorname (firstName) und Sozialversicherungsnummer (socialSecurityNum) basiert. Die letzte Spalte dient zur Unterscheidung von Einwohnern mit gleichem Namen und gleicher Adresse (beispielsweise Vater und Sohn).

|rowkey|       address|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Mit diesem neuen Primärschlüssel werden von Phoenix folgende Zeilenschlüssel generiert:

|rowkey|       address|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

In der ersten Zeile werden die Daten für den Zeilenschlüssel wie folgt dargestellt:

|rowkey|       key|   value| 
|------|--------------------|---|
|  Dole-John-111|address |1111 San Gabriel Dr.|  
|  Dole-John-111|phone |1-425-000-0002|  
|  Dole-John-111|firstName |John|  
|  Dole-John-111|lastName |Dole|  
|  Dole-John-111|socialSecurityNum |111| 

Dieser Zeilenschlüssel speichert nun ein Duplikat der Daten. Berücksichtigen Sie Größe und Anzahl der Spalten, die Sie in den Primärschlüssel aufnehmen, da dieser Wert in jeder Zelle der zugrunde liegenden HBase-Tabelle enthalten ist.

Falls der Primärschlüssel Werte enthält, die sich monoton erhöhen, sollten Sie die Tabelle außerdem mit *Salt-Buckets* erstellen, um die Bildung von Hotspots für Schreibvorgänge zu vermeiden (siehe [Partitionieren von Daten](#partition-data)).

### <a name="column-family-design"></a>Design der Spaltenfamilie

Wenn auf bestimmte Spalten häufiger zugegriffen wird als auf andere, sollten Sie mehrere Spaltenfamilien erstellen, um die häufig verwendeten Spalten von den selten verwendeten zu trennen.

Außerdem empfiehlt es sich, Spalten, auf die in der Regel gemeinsam zugegriffen wird, in der gleichen Spaltenfamilie zu platzieren.

### <a name="column-design"></a>Spaltendesign

* Aufgrund der E/A-Kosten umfangreicher Spalten sollten VARCHAR-Spalten eine Größe von etwa 1 MB nicht übersteigen. Bei der Verarbeitung von Abfragen werden Zellen von HBase vollständig materialisiert, bevor sie an den Client gesendet werden, und der Client empfängt sie vollständig, bevor er sie an den Anwendungscode übergibt.
* Speichern Sie Spaltenwerte in einem kompakten Format wie protobuf, Avro, msgpack oder BSON. Das JSON-Format wird aufgrund seiner Größe nicht empfohlen.
* Komprimieren Sie die Daten vor dem Speichern gegebenenfalls, um die Wartezeit und die E/A-Kosten zu verringern.

### <a name="partition-data"></a>Partitionieren von Daten

Mit Phoenix können Sie die Anzahl von Regionen steuern, an die Ihre Daten verteilt werden, was erhebliche Leistungssteigerungen bei Lese-/Schreibvorgängen ermöglicht. Beim Erstellen einer Phoenix-Tabelle können Sie einen Salt verwenden oder Ihre Daten vorab teilen.

Wenn Sie bei der Tabellenerstellung einen Salt verwenden möchten, geben Sie die Anzahl von Salt-Buckets an:

    CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16

Durch die Verwendung eines Salts wird die Tabelle entlang der Werte von Primärschlüsseln geteilt. (Die Werte werden automatisch ausgewählt.) 

Wenn Sie die Teilung der Tabelle steuern möchten, können Sie die Tabelle vorab teilen, indem Sie die Bereichswerte für die Teilung angeben. Das folgende Beispiel zeigt die Teilung einer Tabelle entlang dreier Regionen:

    CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')

## <a name="index-design"></a>Indexdesign

Ein Phoenix-Index ist eine HBase-Tabelle, die eine Kopie einiger oder aller Daten aus der indizierten Tabelle speichert. Ein Index verbessert die Leistung bei bestimmten Arten von Abfragen.

Wenn Sie mehrere Indizes definiert haben, wählt Phoenix bei einer Tabellenabfrage automatisch den besten Index für die Abfrage. Der primäre Index wird automatisch auf der Grundlage der ausgewählten Primärschlüssel erstellt.

Für zu erwartende Abfragen können Sie auch sekundäre Indizes erstellen, indem Sie die entsprechenden Spalten angeben.

Beachten Sie beim Entwerfen Ihrer Indizes Folgendes:

* Erstellen Sie nur die Indizes, die Sie benötigen.
* Beschränken Sie die Anzahl von Indizes für Tabellen, die häufig aktualisiert werden. Tabellenaktualisierungen gehen mit Schreibvorgängen in der Haupttabelle und in den Indextabellen einher.

## <a name="create-secondary-indexes"></a>Erstellen sekundärer Indizes

Sekundäre Indizes können die Leseleistung verbessern, da anstelle eines vollständigen Tabellenscans eine Punktsuche durchgeführt werden kann. Dies wird allerdings durch Einbußen beim Speicherplatz und bei der Schreibgeschwindigkeit erkauft. Sekundäre Indizes können nach der Tabellenerstellung hinzugefügt und entfernt werden und erfordern keine Anpassung bereits vorhandener Abfragen. Die Abfragen werden lediglich schneller ausgeführt. Erstellen Sie abhängig von Ihren Anforderungen Indizes mit vollständiger Abdeckung, funktionale Indizes oder beides.

### <a name="use-covered-indexes"></a>Verwenden von Indizes mit vollständiger Abdeckung

Indizes mit vollständiger Abdeckung sind Indizes, die zusätzlich zu den indizierten Werten auch Daten aus der Zeile enthalten. Wenn der gewünschte Indexeintrag gefunden wurde, ist kein Zugriff auf die primäre Tabelle erforderlich.

In dem Beispiel mit der Kontakttabelle könnten Sie etwa einen sekundären Index für die Spalte „socialSecurityNum“ erstellen. Dieser sekundäre Index würde zwar Abfragen beschleunigen, bei denen nach der Sozialversicherungsnummer gefiltert wird, zum Abrufen anderer Feldwerte wäre jedoch ein weiterer Lesevorgang in der Haupttabelle erforderlich.

|rowkey|       address|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Wenn Sie jedoch anhand der Sozialversicherungsnummer (socialSecurityNum) in der Regel nach dem Vornamen (firstName) und dem Nachnamen (lastName) suchen, können Sie einen Index mit vollständiger Abdeckung erstellen, der „firstName“ und „lastName“ als tatsächliche Daten in der Indextabelle enthält:

    CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);

Dank dieses Index mit vollständiger Abdeckung kann die folgende Abfrage sämtliche Daten allein durch Lesen der Tabelle mit dem sekundären Index ermitteln:

    SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;

### <a name="use-functional-indexes"></a>Verwenden funktionaler Indizes

Mithilfe funktionaler Indizes können Sie einen Index für einen beliebigen Ausdruck erstellen, von dem Sie erwarten, dass er in Abfragen verwendet wird. Wenn Sie einen funktionalen Index eingerichtet haben und der Ausdruck in einer Abfrage verwendet wird, können die Ergebnisse aus dem Index (anstatt aus der Datentabelle) abgerufen werden.

So können Sie beispielsweise einen Index erstellen, der es ermöglicht, ohne Berücksichtigung der Groß-/Kleinschreibung nach der Kombination aus Vor- und Nachname einer Person zu suchen:

     CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));

## <a name="query-design"></a>Abfragedesign

Beim Abfragedesign sind insbesondere folgende Aspekte entscheidend:

* Verständnis des Abfrageplans und Überprüfung des erwarteten Verhaltens
* Effiziente Verknüpfung

### <a name="understand-the-query-plan"></a>Verständnis des Abfrageplans

Verwenden Sie in [SQLLine](http://sqlline.sourceforge.net/) eine Kombination aus „EXPLAIN“ und Ihrer SQL-Abfrage, um den Plan der Vorgänge anzuzeigen, die von Phoenix ausgeführt werden. Vergewissern Sie sich, dass der Plan folgende Anforderungen erfüllt:

* Er verwendet Ihren Primärschlüssel, wenn dies angebracht ist.
* Er verwendet geeignete sekundäre Indizes anstelle der Datentabelle.
* Er verwendet nach Möglichkeit „RANGE SCAN“ oder „SKIP SCAN“ anstelle von „TABLE SCAN“.

#### <a name="plan-examples"></a>Planbeispiele

Angenommen, Sie verfügen über eine Tabelle namens „FLIGHTS“, in der Informationen zu Flugverspätungen gespeichert werden.

Um alle Flüge mit der Fluggesellschafts-ID `19805` auszuwählen, wenn das Feld „airlineid“ nicht im Primärschlüssel oder in keinem Index enthalten ist, verwenden Sie Folgendes:

    select * from "FLIGHTS" where airlineid = '19805';

Führen Sie den explain-Befehl wie folgt aus:

    explain select * from "FLIGHTS" where airlineid = '19805';

Der Abfrageplan sieht wie folgt aus:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
        SERVER FILTER BY AIRLINEID = '19805'

Beachten Sie in diesem Plan den Ausdruck „FULL SCAN OVER FLIGHTS“. Dieser Ausdruck gibt an, dass ein TABLE SCAN-Vorgang für alle Zeilen durchgeführt wird, anstatt die effizientere Option „RANGE SCAN“ oder „SKIP SCAN“ zu verwenden.

Nehmen wir nun an, Sie möchten für die Fluglinie `AA` eine Abfrage nach Flügen am 2. Januar 2014 mit einer Flugnummer (flightnum) größer 1 durchführen. Nehmen wir weiter an, die Beispieltabelle enthält die Spalten „year“ (Jahr), „month“ (Monat), „dayofmonth“ (Monatstag), „carrier“ (Fluglinie) und „flightnum“ (Flugnummer), und diese Spalten sind alle Teil des zusammengesetzten Primärschlüssels. In diesem Fall sähe die Abfrage wie folgt aus:

    select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Als Nächstes sehen wir uns den Plan für diese Abfrage an:

    explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Der resultierende Plan sieht wie folgt aus:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]

Bei den Werten in eckigen Klammern handelt es sich um den Wertebereich für die Primärschlüssel. In diesem Fall sind die Bereichswerte auf „year = 2014“, „month = 1“ und „dayofmonth = 2“ festgelegt. Für „flightnum“ sind jedoch Werte von 2 aufwärts (`*`) zulässig. Dieser Abfrageplan bestätigt, dass der Primärschlüssel wie erwartet verwendet wird.

Erstellen Sie als Nächstes für die Tabelle „FLIGHTS“ einen Index mit dem Namen `carrier2_idx`, der nur auf dem Feld „carrier“ basiert. Dieser Index enthält auch „flightdate“ (Flugdatum), „tailnum“ (Flugzeugkennung), „origin“ (Herkunft) und „flightnum“ (Flugnummer) als abgedeckte Spalten, deren Daten ebenfalls im Index gespeichert werden.

    CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);

Angenommen, Sie möchten die Fluglinie zusammen mit dem Flugdatum und der Flugzeugkennung abrufen, wie in der folgenden Abfrage gezeigt:

    explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';

Dabei sollte dieser Index verwendet werden:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']

Eine vollständige Liste mit den Elementen, die in den Ergebnissen eines Erläuterungsplans enthalten sein können, finden Sie im [Optimierungsleitfaden für Apache Phoenix](https://phoenix.apache.org/tuning_guide.html) im Abschnitt „Explain Plans“ (Erläuterungspläne).

### <a name="join-efficiently"></a>Effiziente Verknüpfung

Verknüpfungen sollten im Allgemeinen vermieden werden – es sei denn, eine Seite der Verknüpfung ist klein. (Dies gilt insbesondere für häufig durchgeführte Abfragen.)

Bei Bedarf können Sie umfangreiche Verknüpfungen mit dem Hinweis `/*+ USE_SORT_MERGE_JOIN */` implementieren. Umfangreiche Verknüpfungen sind jedoch aufwendige Vorgänge und betreffen eine große Menge von Zeilen. Wenn die Gesamtgröße aller Tabellen auf der rechten Seite den verfügbaren Arbeitsspeicher übersteigen würde, verwenden Sie den Hinweis `/*+ NO_STAR_JOIN */`.

## <a name="scenarios"></a>Szenarien

Die folgenden Richtlinien beschreiben einige allgemeine Muster.

### <a name="read-heavy-workloads"></a>Workloads mit vielen Lesevorgängen

Für Vorgänge mit vielen Lesevorgängen sollten Sie unbedingt Indizes verwenden. Zur Verringerung des Mehraufwands beim Lesen empfiehlt sich ggf. die Erstellung von Indizes mit vollständiger Abdeckung.

### <a name="write-heavy-workloads"></a>Workloads mit vielen Schreibvorgängen

Erstellen Sie für Workloads mit vielen Schreibvorgängen, bei denen sich der Primärschlüssel monoton erhöht, Salt-Buckets, um die Bildung von Hotspots für Schreibvorgänge zu vermeiden. (Aufgrund der zusätzlich erforderlichen Scans müssen Sie hierbei Einbußen beim allgemeinen Lesedurchsatz in Kauf nehmen.) Deaktivieren Sie außerdem „autoCommit“, wenn Sie mit „UPSERT“ eine große Anzahl von Datensätzen schreiben, und fassen Sie die Datensätze zusammen.

### <a name="bulk-deletes"></a>Massenlöschungen

Aktivieren Sie beim Löschen eines großen Datasets „autoCommit“, bevor Sie die DELETE-Abfrage ausgeben, damit der Client nicht die Zeilenschlüssel für alle gelöschten Zeilen speichern muss. „autoCommit“ verhindert, dass der Client die von dem Löschvorgang betroffenen Zeilen puffert. Dadurch kann Phoenix sie direkt auf den Regionsservern löschen, ohne sie an den Client zurückgeben zu müssen.

### <a name="immutable-and-append-only"></a>Unveränderlich, nur anhängen

Wenn Schreibgeschwindigkeit in Ihrem Szenario wichtiger ist als Datenintegrität, können Sie bei der Tabellenerstellung ggf. das Write-Ahead-Protokoll deaktivieren:

    CREATE TABLE CONTACTS (...) DISABLE_WAL=true;

Ausführliche Informationen hierzu und zu anderen Optionen finden Sie unter [Apache Phoenix Grammar](https://phoenix.apache.org/language/index.html#options) (Apache Phoenix-Grammatik).

## <a name="next-steps"></a>Nächste Schritte

* [Optimierungsleitfaden für Apache Phoenix](https://phoenix.apache.org/tuning_guide.html)
* [Secondary Indexing](https://phoenix.apache.org/secondary_indexing.html) (Sekundäre Indizes)
