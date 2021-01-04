---
title: Problembehandlung bei Zuordnungsdatenflüssen
description: In diesem Artikel wird beschrieben, wie Sie in Azure Data Factory Datenflussprobleme beheben.
services: data-factory
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/11/2020
ms.openlocfilehash: 5f29474705919f402b1c114c3fd2df0df037cdae
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696063"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Problembehandlung bei Zuordnungsdatenflüssen in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel werden die gängigen Problembehandlungsmethoden für Zuordnungsdatenflüsse in Azure Data Factory beschrieben.

## <a name="common-errors-and-messages"></a>Häufige Fehler und Meldungen

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Fehlercode: DF-Executor-SourceInvalidPayload
- **Meldung**: Data preview, debug, and pipeline data flow execution failed because container does not exist (Fehler beim Anzeigen von Daten in der Vorschau, beim Debuggen und bei der Datenflussausführung in der Pipeline, weil der Container nicht vorhanden ist)
- **Ursachen**: Das Dataset enthält einen Container, der im Speicher nicht vorhanden ist.
- **Empfehlung**: Stellen Sie sicher, dass der Container, auf den Sie im Dataset verweisen, vorhanden ist und darauf zugegriffen werden kann.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Fehlercode: DF-Executor-SystemImplicitCartesian

- **Meldung**: Implicit cartesian product for INNER join is not supported, use CROSS JOIN instead. (Implizites kartesisches Produkt wird für INNER JOIN nicht unterstützt. Verwenden Sie stattdessen CROSS JOIN.) Die im Join verwendeten Spalten müssen einen eindeutigen Schlüssel für Zeilen erstellen.
- **Ursachen**: Implizites kartesisches Produkt für INNER JOIN zwischen logischen Plänen wird nicht unterstützt. Wenn die im Join verwendeten Spalten den eindeutigen Schlüssel erstellen, ist mindestens eine Spalte von beiden Seiten der Beziehung erforderlich.
- **Empfehlung**: Bei nicht gleichheitsbasierten Joins müssen Sie CUSTOM CROSS JOIN verwenden.

### <a name="error-code-df-executor-systeminvalidjson"></a>Fehlercode: DF-Executor-SystemInvalidJson

- **Meldung**: JSON parsing error, unsupported encoding or multiline (JSON-Analysefehler: nicht unterstützte Codierung oder mehrzeiliger Text)
- **Ursachen**: Mögliche Probleme mit der JSON-Datei: nicht unterstützte Codierung, beschädigte Bytes oder Verwendung der JSON-Quelle als einzelnes Dokument in vielen geschachtelten Zeilen
- **Empfehlung**: Überprüfen Sie, ob die Codierung der JSON-Datei unterstützt wird. Erweitern Sie in der Quelltransformation, die ein JSON-Dataset verwendet, die Option „JSON Settings“ („JSON-Einstellungen“), und aktivieren Sie „Single Document“ („Einzelnes Dokument“).
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Fehlercode: DF-Executor-BroadcastTimeout

- **Meldung**: Broadcast join timeout error, make sure broadcast stream produces data within 60 secs in debug runs and 300 secs in job runs (Übertragungsjoin-Timeoutfehler: Stellen Sie sicher, dass der Übertragungsdatenstrom in Debugausführungen Daten innerhalb von 60 Sekunden und in Auftragsausführungen innerhalb von 300 Sekunden Daten erzeugt.)
- **Ursachen**: Für Übertragungen gilt bei Debugausführungen ein Standardtimeout von 60 Sekunden und bei Auftragsausführungen ein Standardtimeout von 300 Sekunden. Der für die Übertragung ausgewählte Datenstrom ist anscheinend zu groß, um innerhalb dieser Zeitspanne Daten zu erzeugen.
- **Empfehlung**: Überprüfen Sie die Registerkarte „Optimieren“ auf Ihren Datenflusstransformationen auf „Join“, „Exists“ und „Lookup“. Die Standardoption für die Übertragung ist „Auto“. Wenn „Auto“ festgelegt ist oder Sie die linke oder rechte Seite unter „Fixed“ für die Übertragung manuell festlegen, können Sie entweder eine größere Azure Integration Runtime-Konfiguration festlegen oder die Übertragung deaktivieren. Der empfohlene Ansatz für die optimale Leistung bei Datenflüssen besteht darin, Spark zu erlauben, mit „Auto“ zu übertragen und ein arbeitsspeicheroptimiertes Azure IR zu verwenden.

Wenn Sie den Datenfluss in einer Debugtestausführung über eine Debugpipelineausführung ausführen, kann diese Bedingung häufiger auftreten. Dies liegt daran, dass ADF das Übertragungstimeout auf 60 Sekunden einschränkt, um ein schnelleres Debuggen zu gewährleisten. Wenn Sie dies auf das Timeout von 300 Sekunden einer ausgelösten Ausführung verlängern möchten, können Sie die Optionen „Debug“ > „Use Activity Runtime“ auswählen, um die in Ihrer Pipelineaktivität zum Ausführen des Datenflusses definierte Azure IR zu verwenden.

### <a name="error-code-df-executor-conversion"></a>Fehlercode: DF-Executor-Conversion

- **Meldung**: Converting to a date or time failed due to an invalid character (Fehler beim Umwandeln eines Datums oder einer Uhrzeit aufgrund eines ungültigen Zeichens)
- **Ursachen**: Die Daten haben nicht das erwartete Format.
- **Empfehlung**: Verwenden Sie den richtigen Datentyp.

### <a name="error-code-df-executor-invalidcolumn"></a>Fehlercode: DF-Executor-InvalidColumn

- **Meldung**: Column name needs to be specified in the query, set an alias if using a SQL function (Spaltenname muss in der Abfrage angegeben werden. Legen Sie einen Alias fest, wenn Sie eine SQL-Funktion verwenden.)
- **Ursachen**: Es wurde kein Spaltenname angegeben.
- **Empfehlung**: Legen Sie einen Alias fest, wenn Sie SQL-Funktionen wie min()/max() etc. verwenden.

 ### <a name="error-code-df-executor-drivererror"></a>Fehlercode: DF-Executor-DriverError
- **Meldung**: INT96 ist ein älterer Zeitstempeltyp, der vom ADF-Datenfluss nicht unterstützt wird. Sie sollten den Spaltentyp auf die aktuellen Typen aktualisieren.
- **Ursachen**: Treiberfehler
- **Empfehlung**: INT96 ist ein älterer Zeitstempeltyp, der vom ADF-Datenfluss nicht unterstützt wird. Sie sollten den Spaltentyp auf die aktuellen Typen aktualisieren.

 ### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Fehlercode: DF-Executor-BlockCountExceedsLimitError
- **Meldung**: Die Anzahl der Blöcke ohne Commit darf den maximalen Grenzwert von 100.000 Blöcken nicht überschreiten. Überprüfen Sie die Blobkonfiguration.
- **Ursachen**: Ein Blob darf maximal 100.000 Blöcke ohne Commit enthalten.
- **Empfehlung**: Wenden Sie sich bezüglich dieses Problems an das Microsoft-Produktteam, um weitere Informationen zu erhalten.

 ### <a name="error-code-df-executor-partitiondirectoryerror"></a>Fehlercode: DF-Executor-PartitionDirectoryError
- **Meldung**: Der angegebene Quellpfad weist entweder mehrere partitionierte Verzeichnisse (z. B. <Source Path>/<Partitionsstammverzeichnis 1>/a=10/b=20, <Source Path>/<Partitionsstammverzeichnis 2>/c=10/d=30) oder ein partitioniertes Verzeichnis mit einer anderen Datei oder einem nicht partitionierten Verzeichnis auf (z. B. <Source Path>/<Partitionsstammverzeichnis 1>/a=10/b=20, <Source Path>/Verzeichnis 2/Datei1). Entfernen Sie das Partitionsstammverzeichnis aus dem Quellpfad, und lesen Sie es durch eine separate Quelltransformation.
- **Ursachen**: Der Quellpfad weist entweder mehrere partitionierte Verzeichnisse oder ein partitioniertes Verzeichnis mit einer anderen Datei oder einem nicht partitionierten Verzeichnis auf.
- **Empfehlung**: Entfernen Sie das partitionierte Stammverzeichnis aus dem Quellpfad, und lesen Sie es durch eine separate Quelltransformation.

 ### <a name="error-code-df-executor-outofmemoryerror"></a>Fehlercode: DF-Executor-OutOfMemoryError
- **Meldung**: Nicht genügend Arbeitsspeicher während der Ausführung im Cluster verfügbar. Wiederholen Sie den Vorgang mit einer Integration Runtime mit einer größeren Anzahl von Kernen und/oder einem arbeitsspeicheroptimierten Computetyp.
- **Ursachen**: Der Cluster verfügt nicht über genügend Arbeitsspeicher.
- **Empfehlung**: Debugcluster sind für Entwicklungszwecke gedacht. Nutzen Sie Datenstichproben, den entsprechenden Computetyp und die richtige Größe, um die Nutzlast auszuführen. Lesen Sie die [Anleitung zur Leistung und Optimierung der Mapping Data Flow-Funktion](concepts-data-flow-performance.md), um zu erfahren, wie Sie die beste Leistung erzielen können.

 ### <a name="error-code-df-executor-illegalargument"></a>Fehlercode: DF-Executor-illegalArgument
- **Meldung**: Stellen Sie sicher, dass in Ihrem verknüpften Dienst der richtige Zugriffsschlüssel verwendet wird.
- **Ursachen**: Falscher Kontoname oder Zugriffsschlüssel
- **Empfehlung**: Stellen Sie sicher, dass in Ihrem verknüpften Dienst der richtige Kontoname oder Zugriffsschlüssel angegeben ist. 

 ### <a name="error-code-df-executor-invalidtype"></a>Fehlercode: DF-Executor-InvalidType
- **Meldung**: Stellen Sie sicher, dass der Parametertyp mit dem Typ des übergebenen Werts übereinstimmt. Das Übergeben von float-Parametern aus Pipelines wird derzeit nicht unterstützt.
- **Ursachen**: Inkompatible Datentypen zwischen dem deklarierten Typ und dem tatsächlichen Parameterwert
- **Empfehlung**: Vergewissern Sie sich, dass die an einen Datenfluss übergebenen Parameterwerte dem deklarierten Typ entsprechen.

 ### <a name="error-code-df-executor-columnunavailable"></a>Fehlercode: DF-Executor-ColumnUnavailable
- **Meldung**: Der im Ausdruck verwendete Spaltenname ist nicht verfügbar oder ungültig.
- **Ursachen**: Ungültiger oder nicht verfügbarer Spaltenname in Ausdrücken
- **Empfehlung**: Überprüfen Sie die in Ausdrücken verwendeten Spaltennamen.

 ### <a name="error-code-df-executor-parseerror"></a>Fehlercode: DF-Executor-ParseError
- **Meldung**: Der Ausdruck kann nicht analysiert werden.
- **Ursachen**: Der Ausdruck weist Analysefehler aufgrund der Formatierung auf.
- **Empfehlung**: Überprüfen Sie die Formatierung des Ausdrucks.

### <a name="error-code-getcommand-outputasync-failed"></a>Fehlercode: GetCommand OutputAsync failed (Fehler bei GetCommand OutputAsync)

- **Meldung**: During Data Flow debug and data preview: GetCommand OutputAsync failed (Beim Debuggen des Datenflusses und Anzeigen von Daten in der Vorschau: Fehler bei GetCommand OutputAsync mit ...)
- **Ursachen**: Dies ist ein Fehler beim Back-End-Dienst. Sie können den Vorgang wiederholen und auch die Debugsitzung neu starten.
- **Empfehlung**: Wenn das Problem durch Wiederholung und Neustart nicht behoben werden kann, wenden Sie sich an den Kundensupport.

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>Fehlercode: Unerwartete Ausnahme, Fehler bei der Ausführung

- **Meldung**: Während der Ausführung der Datenflussaktivität: Unerwartete Ausnahme, Fehler bei der Ausführung.
- **Ursachen**: Dies ist ein Fehler beim Back-End-Dienst. Sie können den Vorgang wiederholen und auch die Debugsitzung neu starten.
- **Empfehlung**: Wenn das Problem durch Wiederholung und Neustart nicht behoben werden kann, wenden Sie sich an den Kundensupport.

### <a name="error-code-debug-data-preview-no-output-data-on-join"></a>Fehlercode: Debug data preview No Output Data on Join (Debugdatenvorschau – keine Ausgabedaten bei Verknüpfung)

- **Meldung**: There are a high number of null values or missing values which may be caused by having too few rows sampled. Try updating the debug row limit and refreshing the data. (Es ist eine hohe Anzahl von NULL-Werten oder fehlenden Werten vorhanden. Möglicherweise wurden von zu wenigen Zeilen Stichproben genommen. Aktualisieren Sie das Debugzeilenlimit, und aktualisieren Sie die Daten.)
- **Ursachen**: Die Verknüpfungsbedingung wurde von keiner Zeile erfüllt oder bewirkte eine hohe Anzahl von NULL-Werten in der Datenvorschau.
- **Empfehlung**: Wechseln Sie zu den Debugeinstellungen, und erhöhen Sie die maximale Anzahl der Quellzeilen. Stellen Sie sicher, dass Sie für Azure IR einen ausreichend großen Datenflusscluster auswählen, um mehr Daten verarbeiten zu können.

### <a name="error-code-validation-error-at-source-with-multiline-csv-files"></a>Fehlercode: Überprüfungsfehler auf Quelle mit mehrzeiligen CSV-Dateien 

- **Meldung**: Unter Umständen wird eine der folgenden Fehlermeldungen angezeigt:
   - Die letzte Spalte ist NULL oder fehlt.
   - Fehler bei der Schemaüberprüfung auf der Quelle.
   - Der Schemaimport kann auf der Benutzeroberfläche nicht richtig angezeigt werden, und der Name der letzten Spalte enthält ein Neue-Zeile-Zeichen.
- **Ursachen**: Im Zuordnungsdatenfluss funktioniert die mehrzeilige CSV-Quelle derzeit nicht mit „\r\n“ als Zeilentrennzeichen. Zusätzliche Zeilen bei Wagenrückläufen können ggf. zu einer Beschädigung der Quellenwerte führen. 
- **Empfehlung**: Generieren Sie die Datei auf der Quelle mit „\n“ als Zeilentrennzeichen (anstelle von „\r\n“). Alternativ können Sie die Kopieraktivität nutzen, um in der CSV-Datei „\r\n“ durch „\n“ als Zeilentrennzeichen zu ersetzen.

## <a name="general-troubleshooting-guidance"></a>Allgemeine Anleitungen zur Problembehandlung

1. Überprüfen Sie den Status der Datasetverbindungen. Besuchen Sie in jeder Quell-und Senkentransformation den verknüpften Dienst für jedes verwendete Dataset, und testen Sie die Verbindungen.
1. Überprüfen Sie den Status der Datei- und Tabellenverbindungen aus dem Datenfluss-Designer. Aktivieren Sie das Debuggen, und klicken Sie auf „Datenvorschau“ für Ihre Quelltransformationen, um sicherzustellen, dass Sie auf die Daten zugreifen können.
1. Wenn in der Datenvorschau keine Probleme erkennbar sind, wechseln Sie zum Pipeline-Designer, und fügen Sie den Datenfluss in eine Pipelineaktivität ein. Debuggen Sie die Pipeline in einem End-to-End-Test.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung finden Sie in diesen Ressourcen:
*  [Data Factory-Blog](https://techcommunity.microsoft.com/t5/azure-data-factory/bg-p/AzureDataFactoryBlog)
*  [Data Factory-Funktionsanfragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-Videos](https://www.youtube.com/channel/UC2S0k7NeLcEm5_IhHUwpN0g/videos)
*  [Frageseite von Microsoft Q&A (Fragen und Antworten)](/answers/topics/azure-data-factory.html)
*  [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Anleitung zur Leistung und Optimierung der Mapping Data Flow-Funktion](concepts-data-flow-performance.md)
