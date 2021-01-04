---
title: Kompatibilitätsgrade von Azure Stream Analytics
description: Erfahren Sie, wie Sie einen Kompatibilitätsgrad für einen Azure Stream Analytics-Auftrag festlegen und welche größeren Änderungen am neuesten Kompatibilitätsgrad vorgenommen wurden.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 11014c5a5c5cd0cabae1b62083bd5e662be2c6b7
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348932"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Kompatibilitätsgrad für Azure Stream Analytics-Aufträge

In diesem Artikel werden die Optionen für den Kompatibilitätsgrad in Azure Stream Analytics beschrieben. Stream Analytics ist ein verwalteter Dienst mit regelmäßigen Funktionsupdates und Leistungsverbesserungen. Bei den meisten Dienstruntimes sind Updates automatisch für Endbenutzer verfügbar. 

Allerdings können einige neue Funktionen bei einem Dienst zu einer grundlegenden Veränderung führen, z. B. beim Verhalten eines vorhandenen Auftrags oder der Datennutzung ausgeführter Aufträge. Sie können Ihre vorhandenen Stream Analytics-Aufträge ohne wesentliche Änderungen weiter ausführen, indem Sie einen niedrigen Kompatibilitätsgrad festlegen. Wenn Sie die aktuellsten Runtimefunktionen umsetzen möchten, können Sie einfach den Kompatibilitätsgrad erhöhen, um die Änderungen zu übernehmen. 

## <a name="choose-a-compatibility-level"></a>Auswählen eines Kompatibilitätsgrads

Der Kompatibilitätsgrad steuert das Laufzeitverhalten eines Stream Analytics-Auftrags. 

Azure Stream Analytics unterstützt zurzeit drei Kompatibilitätsgrade:

* 1.0 – Ursprünglicher Kompatibilitätsgrad, der mit der allgemeinen Verfügbarkeit von Azure Stream Analytics vor einigen Jahren eingeführt wurde
* 1.1 – Vorheriges Verhalten
* 1.2 – neuestes Verhalten mit den neuesten Verbesserungen

Wenn Sie einen neuen Stream Analytics-Auftrag erstellen, gilt es als bewährte Methode, diesen mit dem neuesten Kompatibilitätsgrad zu erstellen. Starten Sie Ihren Auftragsentwurf mit dem aktuellsten Verhalten, damit Sie später keine Änderungen vornehmen müssen und so die Komplexität erhöhen.

## <a name="set-the-compatibility-level"></a>Festlegen des Kompatibilitätsgrads

Sie können den Kompatibilitätsgrad für einen Stream Analytics-Auftrag über das Azure-Portal oder mithilfe des [REST-API-Aufrufs „create job“](/rest/api/streamanalytics/2016-03-01/streamingjobs/createorreplace#compatibilitylevel) festlegen.

So aktualisieren Sie den Kompatibilitätsgrad des Auftrags im Azure-Portal

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Stream Analytics-Auftrag.
2. **Beenden** Sie den Auftrag, bevor Sie den Kompatibilitätsgrad aktualisieren. Der Kompatibilitätsgrad kann nicht aktualisiert werden, wenn der Auftrag ausgeführt wird.
3. Wählen Sie unter der Überschrift **Konfigurieren** die Option **Kompatibilitätsgrad** aus.
4. Wählen Sie den gewünschten Wert für den Kompatibilitätsgrad aus.
5. Wählen Sie unten auf der Seite **Speichern** aus.

![Kompatibilitätsgrad von Stream Analytics im Azure-Portal](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

Wenn Sie den Kompatibilitätsgrad aktualisieren, überprüft der T-Compiler den Auftrag mit der Syntax, die dem ausgewählten Kompatibilitätsgrad entspricht.

## <a name="compatibility-level-12"></a>Kompatibilitätsgrad 1.2

Beim Kompatibilitätsgrad 1.2 wurden die folgenden grundlegenden Änderungen eingeführt:

###  <a name="amqp-messaging-protocol"></a>AMQP-Messagingprotokoll

**Ebene 1.2** : Azure Stream Analytics verwendet das Nachrichtenprotokoll [Advanced Message Queueing Protocol (AMQP)](../service-bus-messaging/service-bus-amqp-overview.md), um in Service Bus-Warteschlangen und -Themen zu schreiben. AMQP gibt Ihnen die Möglichkeit, plattformübergreifende Hybridanwendungen mit einem offenen Standard zu erstellen.

### <a name="geospatial-functions"></a>Geofunktionen

**Vorherige Ebenen:** In Azure Stream Analytics wurden geografische Berechnungen verwendet.

**Ebene 1.2:** Mit Azure Stream Analytics können Sie geometrisch geplante Geokoordinaten berechnen. Es gibt keine Änderung in der Signatur der Geofunktionen. Ihre Semantik ist jedoch etwas anders, sodass eine genauere Berechnung ermöglicht wird als bisher.

Azure Stream Analytics unterstützt die Indizierung von räumlichen Verweisdaten. Verweisdaten, die Geoelemente enthalten, können für eine schnellere Berechnung von Verknüpfungen indiziert werden.

Die aktualisierten Geofunktionen ermöglichen die volle Ausdrucksfähigkeit des Geoformats Well Known Text (WKT). Sie können andere Geokomponenten angeben, die bisher mit GeoJson nicht unterstützt wurden.

Weitere Informationen finden Sie unter [Updates to geospatial features in Azure Stream Analytics – Cloud and IoT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/) (Aktualisierungen für Geofunktionen in Azure Stream Analytics – Cloud und IoT Edge).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Ausführung paralleler Abfragen für Eingabequellen mit mehreren Partitionen

**Vorherige Ebenen:** Für Azure Stream Analytics-Abfragen war die Verwendung der Klausel „PARTITION BY“ erforderlich, um die Verarbeitung von Abfragen in Partitionen von Eingabequellen zu parallelisieren.

**Ebene 1.2:** Wenn die Abfragelogik in Partitionen von Eingabequellen parallelisiert werden kann, werden in Azure Stream Analytics separate Abfrageinstanzen erstellt und Berechnungen parallel ausgeführt.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Native Integration der Bulk-API in die Cosmos DB-Ausgabe

**Vorherige Ebenen:** Das Upsertverhalten war durch *Einfügen oder Zusammenführen*  definiert.

**Ebene 1.2:** Durch die native Integration der Bulk-API in die Cosmos DB-Ausgabe werden der Durchsatz maximiert und Drosselungsanforderungen effizient verarbeitet. Weitere Informationen finden Sie auf der Seite zur [Azure Stream Analytics-Ausgabe an Azure Cosmos DB](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12).

Das Upsertverhalten ist durch *Einfügen oder Ersetzen*  definiert.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset beim Schreiben in SQL-Ausgabe

**Vorherige Ebenen:** [DateTimeOffset](/sql/t-sql/data-types/datetimeoffset-transact-sql)-Typen wurden an UTC angepasst.

**Ebene 1.2:** DateTimeOffset wird nicht mehr angepasst.

### <a name="long-when-writing-to-sql-output"></a>Long beim Schreiben in SQL-Ausgabe

**Vorherige Ebenen:** Werte wurden je nach Zieltyp abgeschnitten.

**Ebene 1.2:** Werte, die nicht in den Zieltyp passen, werden gemäß der Richtlinie für Ausgabefehler behandelt.

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>Datensatz- und Arrayserialisierung beim Schreiben in die SQL-Ausgabe

**Vorherige Ebenen:** Datensätze wurden als „Datensatz“ und Arrays als „Array“ geschrieben.

**Ebene 1.2:** Datensätze und Arrays werden im JSON-Format serialisiert.

### <a name="strict-validation-of-prefix-of-functions"></a>Strikte Überprüfung des Präfixes von Funktionen

**Vorherige Ebenen:** Es wurde keine strikte Überprüfung von Funktionspräfixen durchgeführt.

**Ebene 1.2:** In Azure Stream Analytics erfolgt eine strikte Überprüfung von Funktionspräfixen. Das Hinzufügen eines Präfixes zu einer integrierten Funktion verursacht einen Fehler. `myprefix.ABS(…)` wird beispielsweise nicht unterstützt.

Auch das Hinzufügen eines Präfixes zu integrierten Aggregaten führt zu einem Fehler. `myprefix.SUM(…)` wird beispielsweise nicht unterstützt.

Die Verwendung des Präfixes „system“ für benutzerdefinierte Funktionen führt zu einem Fehler.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>„Array“ und „Object“ als Schlüsseleigenschaften in Cosmos DB-Ausgabeadapter nicht zugelassen

**Vorherige Ebenen:** Die Typen „Array“ und „Object“ wurden als Schlüsseleigenschaft unterstützt.

**Ebene 1.2:** Die Typen „Array“ und „Object“ werden nicht mehr als Schlüsseleigenschaft unterstützt.

## <a name="compatibility-level-11"></a>Kompatibilitätsgrad 1.1

Beim Kompatibilitätsgrad 1.1 wurden die folgenden grundlegenden Änderungen eingeführt:

### <a name="service-bus-xml-format"></a>Service Bus-XML-Format

**Ebene 1.0:** Azure Stream Analytics verwendete DataContractSerializer, damit der Inhalt der Meldung XML-Tags enthielt. Beispiel:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**Ebene 1.1:** Der Nachrichteninhalt enthält den Datenstrom direkt und ohne weitere Tags. Beispiel: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Beibehalten von Groß-/Kleinbuchstaben für Feldnamen

**Ebene 1.0:** Feldnamen wurden in Kleinbuchstaben umgewandelt, wenn sie vom Azure Stream Analytics-Modul verarbeitet wurden.

**Ebene 1.1:** Die Groß- oder Kleinschreibung für Feldnamen wird beibehalten, während sie vom Azure Stream Analytics-Modul verarbeitet werden.

> [!NOTE]
> Das Beibehalten der Groß- und Kleinschreibung ist für Stream Analytics-Aufträge, die mithilfe der Edge-Umgebung gehostet werden, noch nicht verfügbar. Daher werden alle Feldnamen in Kleinbuchstaben konvertiert, wenn der Auftrag auf Edge gehostet wird.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**Ebene 1.0:** Der Befehl CREATE TABLE filterte nicht nach Ereignissen mit NaN (Not-a-Number, z.B. Infinity, -Infinity) in einer Spalte vom Typ FLOAT, da diese sich außerhalb des dokumentierten Bereichs für diese Zahlen befinden.

**Ebene 1.1:** CREATE TABLE erlaubt die Angabe eines festen Schemas. Das Stream Analytics-Modul überprüft, ob die Daten diesem Schema entsprechen. Mit diesem Modell kann der Befehl Ereignisse mit NaN-Werten filtern.

### <a name="disable-automatic-conversion-of-datetime-strings-to-datetime-type-at-ingress-for-json"></a>Deaktivieren der automatischen Konvertierung von Datetimezeichenfolgen beim Eingang in den DateTime-Typ für JSON

**Ebene 1.0:** Der JSON-Parser würde automatisch Zeichenfolgenwerte mit Datums-/Uhrzeit-/Zoneninformationen beim Eingang in den DATETIME-Typ konvertieren, sodass der Wert sofort seine ursprünglichen Formatierungs- und Zeitzoneninformationen verliert. Da dies beim Eingang erfolgt, selbst wenn dieses Feld nicht in der Abfrage verwendet wurde, wird der Wert in UTC-DateTime konvertiert.

**Ebene 1.1:** Es erfolgt keine automatische Umwandlung von Zeichenfolgenwerten mit Datums-/Uhrzeit-/Zoneninformationen in den Typ DATETIME. Folglich werden Zeitzoneninformationen und die ursprüngliche Formatierung beibehalten. Wenn jedoch das Feld NVARCHAR(MAX) in der Abfrage als Teil eines DATETIME-Ausdrucks (z. B. DATEADD-Funktion) verwendet wird, wird es in den DATETIME-Typ konvertiert, um die Berechnung auszuführen, und verliert sein ursprüngliches Format.

## <a name="next-steps"></a>Nächste Schritte

* [Problembehandlung von Azure Stream Analytics-Eingaben](stream-analytics-troubleshoot-input.md)
* [Stream Analytics-Ressourcenintegrität](./stream-analytics-troubleshoot-query.md)