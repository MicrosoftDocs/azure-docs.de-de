---
title: Erstellen einer IoT-Lösung mit Azure Stream Analytics
description: Enthält ein Tutorial zu den ersten Schritten für die Stream Analytics-IoT-Lösung für ein Mauthäuschen-Szenario.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 87ec59d19fb442293fb7f14d110cf513015ec9f7
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130798"
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Erstellen einer IoT-Lösung mithilfe von Stream Analytics

## <a name="introduction"></a>Einführung
Anhand dieser Lösung wird beschrieben, wie Sie mit Azure Stream Analytics in Echtzeit Einblicke in Ihre Daten erhalten. Entwickler können Datenströme, z.B. Klickstreams, Protokolle und von Geräten generierte Ereignisse, leicht mit Verlaufsdatensätzen oder Referenzdaten kombinieren, um geschäftliche Erkenntnisse zu gewinnen. Da Azure Stream Analytics ein vollständig verwalteter Dienst für die Datenstromberechnung in Echtzeit ist, der in Microsoft Azure gehostet wird, bietet er integrierte Resilienz, Skalierbarkeit sowie geringe Latenz und ist innerhalb von Minuten einsatzbereit.

Nachdem Sie diese Lösung durchgearbeitet haben, verfügen Sie über folgende Kenntnisse:

* Kennenlernen des Azure Stream Analytics-Portals
* Konfigurieren und Bereitstellen eines Streamingauftrags
* Formulieren von realen Problemen und Durchführen der Behebung mithilfe der Stream Analytics-Abfragesprache
* Problemloses Entwickeln von Streaminglösungen für Ihre Kunden mit Stream Analytics
* Verwenden der Überwachung und Protokollierung beim Beheben von Problemen

## <a name="prerequisites"></a>Voraussetzungen
Zum Ausführen der Schritte dieser Lösung benötigen Sie Folgendes:
* [Ein Azure-Abonnement](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>Einführung in das Szenario: „Hallo, Mautstation!“
Eine Mautstation ist eine bekannte Einrichtung. Es gibt sie auf vielen Autobahnen und an Brücken und Tunneln auf der ganzen Welt. Jede Mautstation umfasst mehrere Mauthäuschen. An Häuschen mit manueller Bezahlung halten Sie an und entrichten den Mautbetrag bei der zuständigen Person. Bei Häuschen mit automatisierter Bezahlung wird mit einem Sensor oben auf dem Häuschen eine RFID-Karte abgetastet, die an der Windschutzscheibe Ihres Fahrzeugs angebracht ist, während Sie das Mauthäuschen passieren. Es ist einfach, die Durchfahrt von Fahrzeugen durch diese Mautstellen als einen Strom von Ereignissen zu visualisieren, über den interessante Vorgänge ausgeführt werden können.

![Bild mit Fahrzeugen an Mauthäuschen](media/stream-analytics-build-an-iot-solution-using-stream-analytics/cars-in-toll-booth.jpg)

## <a name="incoming-data"></a>Eingehende Daten
In dieser Lösung werden zwei Datenströme verwendet. Mit Sensoren, die am Eingang und Ausgang der Mautstationen installiert sind, wird der erste Datenstrom produziert. Der zweite Datenstrom ist ein statisches Suchdataset mit Daten zur Fahrzeugregistrierung.

### <a name="entry-data-stream"></a>Eingangsdatenstrom
Der Eingangsdatenstrom enthält Informationen zu den Fahrzeugen, die in Mautstationen einfahren. Die Ereignisse des Ausgangsdatenstroms werden von einer Web-App, die in der Beispiel-App enthalten ist, live in eine Event Hub-Warteschlange gestreamt.

| TollId | EntryTime | LicensePlate | State | Make | Modell | VehicleType | VehicleWeight | Toll | Tag |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

Nachfolgend finden Sie eine kurze Beschreibung der Spalten:

| Column | BESCHREIBUNG |
| --- | --- |
| TollId |Mauthäuschen-ID zur eindeutigen Identifizierung des Mauthäuschens |
| EntryTime |Datum und Uhrzeit der Einfahrt des Fahrzeugs in das Mauthäuschen in UTC |
| LicensePlate |Nummernschild des Fahrzeugs |
| State |Bundesstaat (USA) |
| Make |Der Fahrzeughersteller |
| Modell |Modellnummer des Fahrzeugs |
| VehicleType |1 für Privatfahrzeuge oder 2 für Nutzfahrzeuge |
| WeightType |Fahrzeuggewicht in Tonnen; „0“ für Pkws |
| Toll |Die Mautgebühr in US-Dollar |
| Tag |Nummer des elektronischen Geräts („ETag“) im Fahrzeug, das die automatische Zahlung ermöglicht. Bei nicht-automatischer Zahlung bleibt diese Spalte leer. |

### <a name="exit-data-stream"></a>Ausgangsdatenstrom
Der Ausgangsdatenstrom enthält Informationen zu den Fahrzeugen, die aus der Mautstelle ausfahren. Die Ereignisse des Ausgangsdatenstroms werden von einer Web-App, die in der Beispiel-App enthalten ist, live in eine Event Hub-Warteschlange gestreamt.

| **TollId** | **ExitTime** | **LicensePlate** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.0000000Z |CDE 1007 |

Nachfolgend finden Sie eine kurze Beschreibung der Spalten:

| Column | BESCHREIBUNG |
| --- | --- |
| TollId |Mauthäuschen-ID zur eindeutigen Identifizierung des Mauthäuschens |
| ExitTime |Datum und Uhrzeit der Ausfahrt des Fahrzeugs aus einem Mauthäuschen in UTC |
| LicensePlate |Nummernschild des Fahrzeugs |

### <a name="commercial-vehicle-registration-data"></a>Registrierungsdaten von Nutzfahrzeugen
In der Lösung wird eine statische Momentaufnahme der Registrierungsdatenbank für Nutzfahrzeuge verwendet. Diese Daten werden als JSON-Datei in Azure-Blobspeicher gespeichert, der im Beispiel enthalten ist.

| LicensePlate | RegistrationId | Abgelaufen |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Nachfolgend finden Sie eine kurze Beschreibung der Spalten:

| Column | BESCHREIBUNG |
| --- | --- |
| LicensePlate |Nummernschild des Fahrzeugs |
| RegistrationId |ID der Fahrzeugregistrierung |
| Abgelaufen |Der Registrierungsstatus des Fahrzeugs: „0“, wenn das Fahrzeug registriert ist, und „1“, wenn die Registrierung abgelaufen ist |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Einrichten der Umgebung für Azure Stream Analytics
Sie benötigen ein Microsoft Azure-Abonnement, um diese Lösung durchzuarbeiten. Wenn Sie nicht über ein Azure-Konto verfügen, können Sie eine [kostenlose Testversion anfordern](https://azure.microsoft.com/pricing/free-trial/).

Stellen Sie sicher, dass Sie am Ende dieses Artikels die Anleitung zum Bereinigen Ihres Azure-Kontos befolgen, damit Sie Ihre Azure-Gutschrift bestmöglich nutzen können.

## <a name="deploy-the-sample"></a>Bereitstellen des Beispiels
Es sind verschiedene Ressourcen vorhanden, die mit wenigen Klicks zusammen in einer Ressourcengruppe bereitgestellt werden können. Die Lösungsdefinition wird im GitHub-Repository unter [https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp) gehostet.

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>Bereitstellen der TollApp-Vorlage im Azure-Portal
1. Verwenden Sie den Link zum [Bereitstellen der TollApp-Azure-Vorlage](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json), um die TollApp-Umgebung in Azure bereitzustellen.

2. Melden Sie sich am Azure-Portal an, wenn Sie dazu aufgefordert werden.

3. Wählen Sie das Abonnement aus, unter dem die unterschiedlichen Ressourcen abgerechnet werden.

4. Geben Sie eine neue Ressourcengruppe mit einem eindeutigen Namen an, z.B. `MyTollBooth`.

5. Wählen Sie einen Azure-Standort aus.

6. Geben Sie ein **Intervall** in Sekunden an. Dieser Wert wird in der Beispiel-Web-App verwendet, um anzugeben, wie oft Daten an den Event Hub gesendet werden sollen.

7. **Aktivieren Sie das Kontrollkästchen** , um den Geschäftsbedingungen zuzustimmen.

8. Wählen Sie **An Dashboard anheften** , damit Sie die Ressourcen später leicht finden können.

9. Wählen Sie **Kaufen** , um die Beispielvorlage bereitzustellen.

10. Nach kurzer Zeit wird eine Benachrichtigung mit dem Hinweis **Bereitstellung erfolgreich** angezeigt.

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Überprüfen der Azure Stream Analytics-TollApp-Ressourcen

1. Melden Sie sich auf dem Azure-Portal an.

2. Suchen Sie nach der Ressourcengruppe, der Sie im vorherigen Abschnitt einen Namen gegeben haben.

3. Überprüfen Sie, ob die folgenden Ressourcen in der Ressourcengruppe aufgeführt sind:
   - Ein Cosmos DB-Konto
   - Ein Azure Stream Analytics-Auftrag
   - Ein Azure-Speicherkonto
   - Ein Azure Event Hub
   - Zwei Web-Apps

## <a name="examine-the-sample-tollapp-job"></a>Erkunden des Beispiels für einen TollApp-Auftrag
1. Wählen Sie für die Ressourcengruppe aus dem vorherigen Abschnitt den Stream Analytics-Streamingauftrag aus, der mit dem Namen **tollapp** beginnt (aus Gründen der Eindeutigkeit enthält der Name zufällige Zeichen).

2. Beachten Sie auf der Seite **Übersicht** des Auftrags das Feld **Abfrage** , um die Abfragesyntax anzuzeigen.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   Das Ziel der Abfrage lässt sich auch anhand des folgenden Beispiels darstellen: Angenommen, Sie müssen die Fahrzeuge zählen, die ein Mauthäuschen passieren. Da das Mauthäuschen an einer Straße über einen kontinuierlichen Strom von ankommenden Fahrzeugen verfügt, sind diese Ereignisse mit einem analogen Datenstrom vergleichbar, der niemals endet. Zum Quantifizieren des Datenstroms müssen Sie einen „Zeitraum“ für die Messung definieren. Wir verfeinern die Frage also weiter: „Wie viele Fahrzeuge passieren ein Mauthäuschen jeweils innerhalb von drei Minuten?“ Dies wird für gewöhnlich als „Rollierende Anzahl“ bezeichnet.

   Es ist erkennbar, dass in Azure Stream Analytics eine Abfragesprache verwendet wird, die wie SQL aufgebaut ist und über einige Erweiterungen verfügt, damit die zeitbezogenen Aspekte der Abfrage angegeben werden können.  Weitere Informationen finden Sie in den Artikeln zu [Zeitmanagement](/stream-analytics-query/time-management-azure-stream-analytics)- und [Windowing](/stream-analytics-query/windowing-azure-stream-analytics)-Konstrukten, die in der Abfrage verwendet werden.

3. Untersuchen Sie die Eingaben des TollApp-Beispielauftrags. Nur die EntryStream-Eingabe wird in der aktuellen Abfrage verwendet.
   - Die **EntryStream** -Eingabe ist eine Event Hub-Verbindung, bei der Daten, die jeweils für das Ankommen eines Fahrzeugs am Mauthäuschen einer Straße stehen, in eine Warteschlange eingereiht werden. Eine Web-App, die Teil des Beispiels ist, erstellt die Ereignisse, und diese Daten werden im Event Hub in die Warteschlange eingereiht. Beachten Sie, dass diese Eingabe über die FROM-Klausel der Streamingabfrage abgefragt wird.
   - Die **ExitStream** -Eingabe ist eine Event Hub-Verbindung, bei der Daten, die jeweils für das Ausfahren eines Fahrzeugs aus dem Mauthäuschen einer Straße stehen, in eine Warteschlange eingereiht werden. Diese Streamingeingabe wird in späteren Varianten der Abfragesyntax verwendet.
   - Die Eingabe **Registration** (Registrierung) ist eine Azure-Blobspeicherverbindung und verweist auf die statische Datei „registration.json“, die je nach Bedarf für Suchvorgänge verwendet wird. Diese Referenzdateneingabe wird in späteren Varianten der Abfragesyntax verwendet.

4. Untersuchen Sie die Ausgaben des TollApp-Beispielauftrags.
   - Die **Cosmos DB** -Ausgabe ist ein Cosmos-Datenbankcontainer, der die Ausgabesenkenereignisse empfängt. Beachten Sie, dass diese Ausgabe in der INTO-Klausel der Streamingabfrage verwendet wird.

## <a name="start-the-tollapp-streaming-job"></a>Starten des TollApp-Streamingauftrags
Führen Sie diese Schritte aus, um den Streamingauftrag zu starten:

1. Wählen Sie auf der Seite **Übersicht** des Auftrags die Option **Starten**.

2. Wählen Sie im Bereich **Auftrag starten** die Option **Jetzt**.

3. Wenn der Auftrag ausgeführt wird, können Sie nach kurzer Zeit auf der Seite **Übersicht** des Streamingauftrags den Graphen **Überwachung** anzeigen. Im Graphen sollten mehrere Tausend Eingabeereignisse und Dutzende von Ausgabeereignissen angezeigt werden.

## <a name="review-the-cosmosdb-output-data"></a>Überprüfen der CosmosDB-Ausgabedaten
1. Suchen Sie nach der Ressourcengruppe, die die TollApp-Ressourcen enthält.

2. Wählen Sie das Azure Cosmos DB-Konto mit dem Namensmuster **tollapp\<random\>-cosmos** aus.

3. Wählen Sie die Überschrift **Daten-Explorer** , um die Seite mit dem Daten-Explorer zu öffnen.

4. Erweitern Sie **tollAppDatabase** > **tollAppCollection** > **Dokumente**.

5. In der Liste mit den IDs werden mehrere Dokumente angezeigt, nachdem die Ausgabe verfügbar ist.

6. Wählen Sie jeweils die ID aus, um sich ein JSON-Dokument anzusehen. Beachten Sie jeweils die tollid, die windowend-Zeit und die Fahrzeuganzahl eines Fensters.

7. Nach weiteren drei Minuten ist eine weitere Gruppe von vier Dokumenten verfügbar – ein Dokument pro tollid.


## <a name="report-total-time-for-each-car"></a>Gesamtberichtszeit pro Fahrzeug
Die durchschnittliche Zeit, die ein Fahrzeug zum Passieren des Mauthäuschens benötigt, trägt zur Bewertung der Effizienz des Prozesses und der Benutzerfreundlichkeit für Kunden bei.

Verknüpfen Sie zum Ermitteln der Gesamtzeit den EntryTime-Datenstrom mit dem ExitTime-Datenstrom. Verknüpfen Sie die beiden Eingabedatenströme für die übereinstimmenden Spalten TollId und LicencePlate. Beim Operator **JOIN** müssen Sie einen zeitlichen Spielraum angeben, um die akzeptable Zeitdifferenz zwischen den verknüpften Ereignissen zu beschreiben. Verwenden Sie die Funktion **DATEDIFF** , um anzugeben, dass Ereignisse nicht mehr als 15 Minuten auseinander liegen sollen. Wenden Sie die Funktion **DATEDIFF** außerdem auf Ausfahrts- und Einfahrtszeiten an, um die genaue Zeit zu berechnen, die ein Fahrzeug in der Mautstation verbringt. Beachten Sie den Unterschied bei der Verwendung der Funktion **DATEDIFF** in einer **SELECT** -Anweisung im Vergleich zu einer **JOIN** -Bedingung.

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>Aktualisieren Sie die Abfragesyntax des TollApp-Streamingauftrags wie folgt:

1. Wählen Sie auf der Seite **Übersicht** des Auftrags die Option **Beenden**.

2. Warten Sie kurz auf die Benachrichtigung, dass der Auftrag beendet wurde.

3. Wählen Sie unter der Überschrift „AUFTRAGSTOPOLOGIE“ die Option **< > Abfrage**.

4. Fügen Sie die angepasste SQL-Streamingabfrage ein.

5. Wählen Sie **Speichern** , um die Abfrage zu speichern. Bestätigen Sie den Vorgang mit **Ja** , um die Änderungen zu speichern.

6. Wählen Sie auf der Seite **Übersicht** des Auftrags die Option **Starten**.

7. Wählen Sie im Bereich **Auftrag starten** die Option **Jetzt**.

### <a name="review-the-total-time-in-the-output"></a>Überprüfen der Gesamtzeit in der Ausgabe
Wiederholen Sie die Schritte im vorherigen Abschnitt, um die CosmosDB-Ausgabedaten aus dem Streamingauftrag zu überprüfen. Überprüfen Sie die neuesten JSON-Dokumente.

In diesem Dokument werden beispielsweise ein Beispielfahrzeug mit einem bestimmten Nummernschild, der Einfahr- und Ausfahrzeitpunkt und das berechnete DATEDIFF-Feld „durationinminutes“ mit einer Verweildauer von zwei Minuten angezeigt:
```JSON
{
    "tollid": 4,
    "entrytime": "2018-04-05T06:51:39.0491173Z",
    "exittime": "2018-04-05T06:53:09.0491173Z",
    "licenseplate": "JVR 9425",
    "durationinminutes": 2,
    "id": "ff52eb25-d580-7566-2879-1f52bba6601e",
    "_rid": "+8E4AI1DZgBjAAAAAAAAAA==",
    "_self": "dbs/+8E4AA==/colls/+8E4AI1DZgA=/docs/+8E4AI1DZgBjAAAAAAAAAA==/",
    "_etag": "\"ad02f6b8-0000-0000-0000-5ac5c8330000\"",
    "_attachments": "attachments/",
    "_ts": 1522911283
}
```

## <a name="report-vehicles-with-expired-registration"></a>Melden von Fahrzeugen mit abgelaufener Registrierung
Azure Stream Analytics kann statische Momentaufnahmen von Referenzdaten mit temporären Datenströmen verknüpfen. Um diese Funktion zu demonstrieren, verwenden wir die folgende Beispielfrage. Die Eingabe „Registration“ (Registrierung) ist eine statische JSON-Blobdatei, in der der Ablauf von Nummernschildmarkierungen aufgeführt ist. Wenn dies mit dem Nummernschild verknüpft wird, werden die Referenzdaten für jedes Fahrzeug verglichen, das das Mauthäuschen passiert.

Wenn ein Nutzfahrzeug bei einer Mautfirma registriert ist, kann es das Mauthäuschen passieren, ohne für eine Kontrolle angehalten zu werden. Verwenden Sie die Nachschlagetabellen für die Registrierung, um alle Nutzfahrzeuge mit abgelaufener Registrierung zu identifizieren.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. Wiederholen Sie die Schritte des vorherigen Abschnitts, um die Abfragesyntax des TollApp-Streamingauftrags zu aktualisieren.

2. Wiederholen Sie die Schritte im vorherigen Abschnitt, um die CosmosDB-Ausgabedaten aus dem Streamingauftrag zu überprüfen.

Beispielausgabe:
```json
    {
        "entrytime": "2018-04-05T08:01:28.0252168Z",
        "licenseplate": "GMT 3221",
        "tollid": 1,
        "registrationid": "763220582",
        "id": "47db0535-9716-4eb2-db58-de7886966cbf",
        "_rid": "y+F8AJ9QWACSAQAAAAAAAA==",
        "_self": "dbs/y+F8AA==/colls/y+F8AJ9QWAA=/docs/y+F8AJ9QWACSAQAAAAAAAA==/",
        "_etag": "\"88007d8d-0000-0000-0000-5ac5d7e20000\"",
        "_attachments": "attachments/",
        "_ts": 1522915298
    }
```

## <a name="scale-out-the-job"></a>Aufskalieren des Auftrags
Azure Stream Analytics ist für die elastische Skalierung ausgelegt, damit große Datenmengen verarbeitet werden können. Die Azure Stream Analytics-Abfrage kann eine **PARTITION BY** -Klausel verwenden, um das System darauf hinzuweisen, dass für diesen Schritt horizontal hochskaliert wird. **PartitionId** ist eine spezielle Spalte, die vom System hinzugefügt wurde und mit der Partitions-ID der Eingabe (Event Hub) übereinstimmt.

Ändern Sie die Abfragesyntax in den folgenden Code, um die Abfrage für Partitionen aufzuskalieren:
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream
TIMESTAMP BY EntryTime
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

Skalieren Sie den Streamingauftrag wie folgt auf mehr Streamingeinheiten hoch:

1. **Beenden** Sie den aktuellen Auftrag.

2. Aktualisieren Sie die Abfragesyntax auf der Seite **< > Abfrage** , und speichern Sie die Änderungen.

3. Wählen Sie unter der Überschrift „KONFIGURIEREN“ des Streamingauftrags die Option **Skalieren**.

4. Verschieben Sie den Schieberegler **Streamingeinheiten** von 1 auf 6. Mit den Streamingeinheiten wird die Menge an Rechenleistung definiert, die der Auftrag erhalten kann. Wählen Sie **Speichern** aus.

5. **Starten** Sie den Streamingauftrag, um die zusätzliche Skalierung zu demonstrieren. Azure Stream Analytics erreicht eine Verteilung der Arbeit auf mehr Computeressourcen und einen besseren Durchsatz, indem die Arbeit über die Spalte, die in der PARTITION BY-Klausel angegeben ist, ressourcenübergreifend partitioniert wird.

## <a name="monitor-the-job"></a>Überwachen des Auftrags
Der Bereich **MONITOR** (ÜBERWACHEN) enthält Statistiken zum laufenden Auftrag. Um das Speicherkonto in der gleichen Region zu verwenden, ist eine erstmalige Konfiguration erforderlich (die Mautstation muss wie der Rest des Dokuments bezeichnet werden).

![Azure Stream Analytics-Auftragsüberwachung](media/stream-analytics-build-an-iot-solution-using-stream-analytics/stream-analytics-job-monitoring.png)

Sie können auf die **Aktivitätsprotokolle** auch über das Dashboard für Aufträge im Bereich **Einstellungen** zugreifen.

## <a name="clean-up-the-tollapp-resources"></a>Bereinigen der TollApp-Ressourcen
1. Beenden Sie den Stream Analytics-Auftrag im Azure-Portal.

2. Suchen Sie nach der Ressourcengruppe, die in Bezug auf die TollApp-Vorlage acht Ressourcen enthält.

3. Wählen Sie die Option **Ressourcengruppe löschen**. Geben Sie den Namen der Ressourcengruppe ein, um den Löschvorgang zu bestätigen.

## <a name="conclusion"></a>Zusammenfassung
In dieser Lösung haben Sie eine Einführung in den Azure Stream Analytics-Dienst erhalten. Es wurde gezeigt, wie Sie Eingaben und Ausgaben für den Stream Analytics-Auftrag konfigurieren. Anhand des Mautdatenszenarios wurden die häufigsten Probleme erläutert, die im Bereich der Daten in Bewegung vorkommen, und es wurde aufgezeigt, wie diese mithilfe von einfachen SQL-ähnlichen Abfragen in Azure Stream Analytics behoben werden können. In der Lösung wurden SQL-Erweiterungskonstrukte für die Arbeit mit temporären Datenströmen beschrieben. Es wurde veranschaulicht, wie Sie Datenströme verknüpfen, den Datenstrom um statische Referenzdaten erweitern und eine Abfrage aufskalieren, um einen höheren Durchsatz zu erzielen.

Die Lösung ist zwar eine gute Einführung, aber bei Weitem nicht erschöpfend. Weitere Abfragemuster mit der SAQL-Sprache finden Sie unter [Abfragebeispiele für gängige Stream Analytics-Verwendungsmuster](stream-analytics-stream-analytics-query-patterns.md).