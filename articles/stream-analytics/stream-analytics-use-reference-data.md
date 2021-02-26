---
title: Verwenden von Referenzdaten für Suchvorgänge in Azure Stream Analytics
description: In diesem Artikel wird beschrieben, wie Sie Referenzdaten nutzen, um nach Daten in einem Abfragedesign eines Azure Stream Analytics-Auftrags zu suchen oder diese zu korrelieren.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: e05a4cbbc5fefbfe8a92914ef480f32bdf43ca37
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99560218"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Verwenden von Referenzdaten für Suchvorgänge in Stream Analytics

Verweisdaten (auch als Nachschlagetabelle bezeichnet) stellen ein begrenztes statisches oder sich nur langsam veränderndes Dataset dar, das für die Suche oder die Erweiterung Ihrer Datenströme verwendet wird. In einem IoT-Szenario können Sie beispielsweise Metadaten zu Sensoren (die sich nicht oft ändern) in Verweisdaten speichern und mit IoT-Echtzeitdatenströmen verknüpfen. Azure Stream Analytics lädt Verweisdaten in den Arbeitsspeicher, um eine Streamverarbeitung mit geringer Wartezeit zu erreichen. Für den Einsatz von Verweisdaten in Ihrem Azure Stream Analytics-Auftrag verwenden Sie in der Regel [Verweisdaten für JOIN-Vorgänge](/stream-analytics-query/reference-data-join-azure-stream-analytics) in Ihrer Abfrage. 

## <a name="example"></a>Beispiel  
Sie können beispielsweise in Echtzeit einen Ereignisdatenstrom generieren, wenn Fahrzeuge eine Mautstelle passieren. An der Mautstelle kann das Autokennzeichen in Echtzeit erfasst und mit einem statischen Dataset verknüpft werden, das über Registrierungsdetails verfügt. So lassen sich die abgelaufenen Kennzeichen identifizieren.  
  
```SQL  
SELECT I1.EntryTime, I1.LicensePlate, I1.TollId, R.RegistrationId  
FROM Input1 I1 TIMESTAMP BY EntryTime  
JOIN Registration R  
ON I1.LicensePlate = R.LicensePlate  
WHERE R.Expired = '1'
```  

In Stream Analytics werden Azure Blob Storage und Azure SQL-Datenbank als Speicherebene für Verweisdaten unterstützt. Zudem können Sie Verweisdaten von Azure Data Factory in Blob Storage transformieren und/oder kopieren, um [eine beliebige Anzahl von cloudbasierten und lokalen Datenspeichern zu verwenden](../data-factory/copy-activity-overview.md).

## <a name="azure-blob-storage"></a>Azure Blob Storage

Referenzdaten werden als (in der Eingabekonfiguration definierte) Blobsequenz in aufsteigender Reihenfolge nach dem im Blobnamen angegebenen Datums- bzw. Uhrzeitwert modelliert. Hinzufügungen sind jeweils **nur** am Sequenzende möglich. Hierzu muss der verwendete Datums-/Uhrzeitwert den Wert des letzten Blobs in der Sequenz **übersteigen**. Weitere Informationen finden Sie unter [Schutz von Daten in Azure Stream Analytics](data-protection.md).

### <a name="configure-blob-reference-data"></a>Konfigurieren von Blobverweisdaten

Um die Verweisdaten zu konfigurieren, müssen Sie zunächst eine Eingabe vom Typ **Verweisdaten** erstellen. Die folgende Tabelle enthält den Namen jeder Eigenschaft, die Sie beim Erstellen der Verweisdateneingabe angeben müssen, sowie die entsprechenden Beschreibungen:

|**Eigenschaftenname**  |**Beschreibung**  |
|---------|---------|
|Eingabealias   | Ein Anzeigename, der in der Auftragsabfrage verwendet wird, um auf diese Eingabe zu verweisen.   |
|Speicherkonto   | Der Name des Speicherkontos, in dem sich Ihre Blobs befinden. Wenn sich das Konto im gleichen Abonnement befindet wie Ihr Stream Analytics-Auftrag, können Sie es in der Dropdownliste auswählen.   |
|Speicherkontoschlüssel   | Der geheime Schlüssel, der dem Speicherkonto zugeordnet ist. Dies wird automatisch aufgefüllt, wenn sich das Speicherkonto im selben Abonnement befindet wie Ihr Stream Analytics-Auftrag.   |
|Speichercontainer   | Container stellen eine logische Gruppierung für Blobs bereit, die im Microsoft Azure-Blobdienst gespeichert sind. Wenn Sie ein Blob in den Blobdienst hochladen, müssen Sie einen Container für das Blob angeben.   |
|Pfadmuster   | Dies ist eine erforderliche Eigenschaft, mit der Ihre Blobs im angegebenen Container gesucht werden. In dem Pfad können Sie mindestens eine Instanz der folgenden beiden Variablen angeben:<BR>{date}, {time}<BR>Beispiel 1: products/{date}/{time}/product-list.csv<BR>Beispiel 2: products/{date}/product-list.csv<BR>Beispiel 3: product-list.csv<BR><br> Wenn das Blob im angegebenen Pfad nicht vorhanden ist, wartet der Stream Analytics-Auftrag auf unbestimmte Zeit, bis das Blob verfügbar ist.   |
|Datumsformat [optional]   | Wenn Sie innerhalb des von Ihnen angegebenen Pfadmusters „{date}“ verwendet haben, können Sie in der Dropdownliste mit den unterstützten Formaten das Datumsformat auswählen, in dem Ihre Blobs gespeichert werden sollen.<BR>Beispiel: YYYY/MM/DD, MM/DD/YYYY...   |
|Zeitformat [optional]   | Wenn Sie innerhalb des von Ihnen angegebenen Pfadmusters „{time}“ verwendet haben, können Sie in der Dropdownliste mit den unterstützten Formaten das Zeitformat auswählen, in dem Ihre Blobs gespeichert werden sollen.<BR>Beispiel: HH, HH/mm, HH-mm  |
|Ereignisserialisierungsformat   | Um sicherzustellen, dass Ihre Abfragen wie erwartet funktionieren, muss Stream Analytics das Serialisierungsformat kennen, das Sie für eingehende Datenströme verwenden. Die unterstützten Formate für Verweisdaten sind CSV und JSON.  |
|Codieren   | UTF-8 ist derzeit das einzige unterstützte Codierungsformat.  |

### <a name="static-reference-data"></a>Statische Referenzdaten

Wenn sich Ihre Referenzdaten voraussichtlich nicht ändern, wird die Unterstützung für statische Referenzdaten aktiviert, indem in der Eingabekonfiguration ein statischer Pfad angegeben wird. Azure Stream Analytics findet das Blob unter dem angegebenen Pfad. {date}- und {time}-Ersetzungstoken sind nicht erforderlich. Da Verweisdaten in Stream Analytics unveränderlich sind, wird nicht empfohlen, statische Verweisdatenblobs zu überschreiben.

### <a name="generate-reference-data-on-a-schedule"></a>Generieren von Verweisdaten nach einem Zeitplan

Wenn es sich bei Ihren Verweisdaten um ein sich langsam änderndes Dataset handelt, wird die Unterstützung für das Aktualisieren von Verweisdaten aktiviert, indem Sie in der Eingabekonfiguration ein Pfadmuster mit den Ersetzungstoken „{date}“ und „{time}“ angeben. Stream Analytics ruft die aktualisierten Definitionen von Verweisdaten auf der Grundlage dieses Pfadmusters ab. Beispiel: Das Muster `sample/{date}/{time}/products.csv` mit dem Datumsformat **YYYY-MM-DD** und dem Zeitformat **HH-mm** weist Stream Analytics an, das aktualisierte Blob `sample/2015-04-16/17-30/products.csv` am 16. April 2015 um 17:30 (UTC-Zeitzone) abzurufen.

Azure Stream Analytics führt in einem Intervall von einer Minute automatisch einen Scan für aktualisierte Referenzdatenblobs durch. Wenn ein Blob mit Zeitstempel 10:30:00 mit einer kleinen Verzögerung hochgeladen wird (z. B. 10:30:30), werden Sie eine kleine Verzögerung im Stream Analytics-Auftrag bemerken, der auf dieses Blob verweist. Zur Vermeidung solcher Szenarien wird empfohlen, das Blob früher als die angestrebte effektive Zeit (in diesem Beispiel 10:30:00 Uhr) hochzuladen, damit der Stream Analytics-Auftrag ausreichend Zeit hat, es zu entdecken und in den Speicher zu laden und Vorgänge durchzuführen. 

> [!NOTE]
> Stream Analytics-Aufträge suchen derzeit nur dann nach der Blobaktualisierung, wenn die Zeit des Computers die im Blobnamen codierte Zeit erreicht. Der Auftrag sucht beispielsweise am 16. April 2015 ab 17:30 Uhr (UTC-Zeitzone) zum frühestmöglichen Zeitpunkt nach `sample/2015-04-16/17-30/products.csv`. Er sucht *nie* nach einem Blob mit einer codierten Zeit vor der letzten Erkennung.
> 
> Nachdem der Auftrag das Blob `sample/2015-04-16/17-30/products.csv` gefunden hat, werden alle Dateien ignoriert, deren codierte Zeit vor 17:30 Uhr am 16. April 2015 liegt. Wenn im gleichen Container also nachträglich das Blob `sample/2015-04-16/17-25/products.csv` erstellt wird, verwendet der Auftrag es nicht.
> 
> Analog dazu gilt: Wenn `sample/2015-04-16/17-30/products.csv` erst am 16. April 2015 um 22:03 Uhr erstellt wird und im Container kein Blob mit einer früheren Zeit enthalten ist, verwendet der Auftrag diese Datei ab dem 16. April 2015, 22:03 Uhr. Bis dahin werden die vorherigen Verweisdaten verwendet.
> 
> Eine Ausnahme besteht darin, wenn der Auftrag Daten rückwirkend verarbeiten muss oder wenn der Auftrag zum ersten Mal gestartet wird. Zum Startzeitpunkt sucht der Auftrag nach dem aktuellsten Blob, das vor der angegebenen Startzeit des Auftrags erstellt wurde. Damit wird sichergestellt, dass beim Starten des Auftrags ein **nicht leeres** Verweisdataset zur Verfügung steht. Sollte keines gefunden werden, erscheint die folgende Diagnose: `Initializing input without a valid reference data blob for UTC time <start time>`.

Mit [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) kann das Erstellen der aktualisierten Blobs orchestriert werden, die von Stream Analytics zur Aktualisierung der Verweisdatendefinitionen benötigt werden. Data Factory ist ein cloudbasierter Daten-Integrationsdienst, der das Verschieben und Transformieren von Daten organisiert und automatisiert. Data Factory unterstützt [die Verbindung zu einer großen Anzahl von cloudbasierten und lokalen Datenspeichern](../data-factory/copy-activity-overview.md) und das mühelose Verschieben von Daten in regelmäßigen von Ihnen festgelegten Abständen. Weitere Informationen sowie eine schrittweise Anleitung zum Einrichten einer Data Factory-Pipeline zum Generieren von Verweisdaten für Stream Analytics, die nach einem vordefinierten Zeitplan aktualisiert werden, finden Sie in diesem [GitHub-Beispiel](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs).

### <a name="tips-on-refreshing-blob-reference-data"></a>Tipps zum Aktualisieren von Blobverweisdaten

1. Überschreiben Sie Referenzdatenblobs nicht, da sie unveränderlich sind.
2. Die empfohlene Methode zum Aktualisieren von Referenzdaten lautet:
    * Verwenden von {date}/{time} im Pfadmuster
    * Hinzufügen eines neuen Blobs, indem Sie denselben Container und das in der Auftragseingabe definierte Pfadmuster verwenden
    * Verwenden eines date/time-Werts, der **größer** als der vom letzten Blob in der Sequenz angegebene Wert ist
3. Verweisdatenblobs werden **nicht** nach der Zeit „Zuletzt geändert“ des Blobs sortiert, sondern nur nach den Werten für Uhrzeit und Datum, die im Blobnamen mithilfe der Ersetzungen {date} und {time} angegeben werden.
3. Um die Auflistung einer großen Anzahl von Blobs zu vermeiden, sollten Sie darüber nachdenken, sehr alte Blobs zu löschen, für die die Verarbeitung nicht mehr durchgeführt wird. Beachten Sie, dass ASA in einigen Szenarien, wie z.B. einem Neustart, eine kleine Menge nachbearbeiten muss.

## <a name="azure-sql-database"></a>Azure SQL-Datenbank

Azure SQL-Datenbank-Verweisdaten werden durch den Stream Analytics-Auftrag abgerufen und zur Verarbeitung als Momentaufnahme im Speicher gespeichert. Die Momentaufnahme der Verweisdaten wird zudem in einem Container in einem Speicherkonto gespeichert, das Sie in den Konfigurationseinstellungen angeben. Der Container wird beim Starten des Auftrags automatisch erstellt. Wenn der Auftrag beendet wird oder in den fehlerhaften Zustand übergeht, werden die automatisch erstellten Container gelöscht, sobald der Auftrag neu gestartet wird.  

Wenn es sich bei den Verweisdaten um ein sich langsam änderndes Dataset handelt, müssen Sie die im Auftrag verwendete Momentaufnahme in regelmäßigen Abständen aktualisieren. In Stream Analytics können Sie beim Konfigurieren der Azure SQL-Datenbank-Eingabeverbindung eine Aktualisierungsrate festlegen. Die Stream Analytics-Runtime fragt die Azure SQL-Datenbank-Instanz in dem durch die Aktualisierungsrate angegebenen Intervall ab. Als schnellste Aktualisierungsrate wird einmal pro Minute unterstützt. Für jede Aktualisierung wird in Stream Analytics eine neue Momentaufnahme im angegebenen Speicherkonto gespeichert.

Stream Analytics umfasst zwei Möglichkeiten zum Abfragen von Azure SQL-Datenbank. Eine Momentaufnahmeabfrage ist obligatorisch und muss in jedem Auftrag enthalten sein. In Stream Analytics wird die Momentaufnahmeabfrage regelmäßig basierend auf dem Aktualisierungsintervall durchgeführt, und das Ergebnis der Abfrage (die Momentaufnahme) wird als Verweisdataset verwendet. Die Momentaufnahmeabfrage sollte sich für die meisten Szenarien eignen. Wenn jedoch bei großen Datasets und schnellen Aktualisierungsraten Leistungsprobleme auftreten, können Sie die Option für die Deltaabfrage verwenden. Abfragen, die mehr als 60 Sekunden benötigen, um ein Verweisdataset zurückzugeben, führen zu einem Timeout.

Mit der Deltaabfrage wird in Stream Analytics zunächst die Momentaufnahmeabfrage durchgeführt, um ein Basis-Verweisdataset abzurufen. Anschließend wird regelmäßig die Deltaabfrage gemäß dem Aktualisierungsintervall durchgeführt, um inkrementelle Änderungen abzurufen. Diese inkrementellen Änderungen werden fortlaufend auf das Verweisdataset angewandt, um es auf dem neuesten Stand zu halten. Durch Verwendung von Deltaabfragen können sich die Speicherkosten und E/A-Netzwerkvorgänge verringern.

### <a name="configure-sql-database-reference"></a>Konfigurieren der SQL-Datenbank-Verweisdaten

Um die SQL-Datenbank-Verweisdaten zu konfigurieren, müssen Sie zunächst eine Eingabe für **Verweisdaten** erstellen. Die folgende Tabelle enthält den Namen jeder Eigenschaft, die Sie beim Erstellen der Verweisdateneingabe angeben müssen, sowie die entsprechenden Beschreibungen. Weitere Informationen finden Sie unter [Verwenden von Verweisdaten aus einer SQL-Datenbank für einen Azure Stream Analytics-Auftrag](sql-reference-data.md).

Sie können eine [verwaltete Azure SQL-Instanz](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) als Verweisdateneingabe verwenden. Sie müssen einen [öffentlichen Endpunkt in der verwalteten SQL-Instanz konfigurieren](../azure-sql/managed-instance/public-endpoint-configure.md) und dann in Azure Stream Analytics manuell die folgenden Einstellungen konfigurieren. Für den virtuellen Azure-Computer mit SQL Server und einer angefügten Datenbank wird ebenfalls das manuelle Konfigurieren der folgenden Einstellungen unterstützt.

|**Eigenschaftenname**|**Beschreibung**  |
|---------|---------|
|Eingabealias|Ein Anzeigename, der in der Auftragsabfrage verwendet wird, um auf diese Eingabe zu verweisen.|
|Subscription|Auswählen Ihres Abonnements|
|Datenbank|Die Azure SQL-Datenbank mit den Verweisdaten. Für eine verwaltete SQL-Instanz muss der Port 3342 angegeben werden. Beispiel: *sampleserver.public.database.windows.net,3342*|
|Username|Der Benutzername, der Ihrer Azure SQL-Datenbank-Instanz zugeordnet ist.|
|Kennwort|Das Kennwort, das Ihrer Azure SQL-Datenbank-Instanz zugeordnet ist.|
|Regelmäßig aktualisieren|Mit dieser Option können Sie eine Aktualisierungsrate auswählen. Durch Auswählen von „Ein“ können Sie die Aktualisierungsrate im Format TT:HH:MM angeben.|
|Momentaufnahmeabfrage|Dies ist die Standardabfrageoption, über die die Verweisdaten von der SQL-Datenbank abgerufen werden.|
|Deltaabfrage|Wählen Sie diese Option aus, um für erweiterte Szenarien mit großen Datasets und einer kurzen Aktualisierungsrate eine Deltaabfrage hinzuzufügen.|

## <a name="size-limitation"></a>Größenbeschränkung

Es wird empfohlen, Verweisdatasets zu verwenden, die kleiner als 300 MB sind, um eine optimale Leistung zu erzielen. Verweisdatasets mit bis zu 5 GB werden in Aufträgen mit mindestens sechs SUs unterstützt. Die Verwendung von sehr großen Verweisdaten kann sich auf die End-to-End-Wartezeit Ihres Auftrags auswirken. Angesichts der zunehmenden Abfragekomplexität durch Einbeziehung der zustandsbehafteten Verarbeitung (beispielsweise in Form von Aggregaten im Fenstermodus, temporalen Joins oder temporalen Analysefunktionen) ist davon auszugehen, dass die unterstützte Maximalgröße für Verweisdaten abnimmt. Wenn Azure Stream Analytics die Verweisdaten nicht laden und keine komplexen Vorgänge ausführen kann, steht für den Auftrag nicht genügend Arbeitsspeicher zur Verfügung, und der Auftrag ist nicht erfolgreich. In solchen Fällen erreicht die Metrik „Nutzung der Speichereinheit in %“ den Wert „100 %“.    

|**Anzahl von Streamingeinheiten**  |**Empfohlene Größe**  |
|---------|---------|
|1   |50 MB oder weniger   |
|3   |150 MB oder weniger   |
|6 und mehr   |5 GB oder weniger    |

Die Unterstützung der Komprimierung steht für Referenzdaten nicht zur Verfügung.

## <a name="joining-multiple-reference-datasets-in-a-job"></a>Verknüpfen mehrerer Verweisdatasets in einem Auftrag
In einem einzelnen Schritt der Abfrage können Sie nur eine Datenflusseingabe mit einer Verweisdateneingabe verknüpfen. Wenn Sie die Abfrage in mehrere Schritte aufteilen, können Sie jedoch auch mehrere Verweisdatasets verknüpfen. Ein entsprechendes Beispiel ist nachfolgend dargestellt.

```SQL  
With Step1 as (
    --JOIN input stream with reference data to get 'Desc'
    SELECT streamInput.*, refData1.Desc as Desc
    FROM    streamInput
    JOIN    refData1 ON refData1.key = streamInput.key 
)
--Now Join Step1 with second reference data
SELECT *
INTO    output 
FROM    Step1
JOIN    refData2 ON refData2.Desc = Step1.Desc 
``` 

## <a name="iot-edge-jobs"></a>IoT Edge-Aufträge

Für Stream Analytics-Edge-Aufträge werden nur lokale Verweisdaten unterstützt. Wenn ein Auftrag auf einem IoT Edge-Gerät bereitgestellt wird, werden Verweisdaten über den benutzerdefinierten Dateipfad geladen. Halten Sie auf dem Gerät eine Verweisdatendatei bereit. Ordnen Sie die Verweisdatendatei für einen Windows-Container auf dem lokalen Laufwerk an, und geben Sie das lokale Laufwerk mit dem Docker-Container frei. Erstellen Sie für einen Linux-Container ein Docker-Volume, und fügen Sie die Datendatei auf dem Volume ein.

Die Aktualisierung der Verweisdaten in IoT Edge wird durch eine Bereitstellung ausgelöst. Nach dem Auslösen wählt das Stream Analytics-Modul die aktualisierten Daten aus, ohne die Ausführung des Auftrags zu beenden.

Es gibt zwei Möglichkeiten, die Verweisdaten zu aktualisieren:

* Aktualisieren des Verweisdatenpfads für Ihren Stream Analytics-Auftrag im Azure-Portal.

* Aktualisieren der IoT Edge-Bereitstellung

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Schnellstart: Erstellen eines Stream Analytics-Auftrags mithilfe des Azure-Portals](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: ./stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
