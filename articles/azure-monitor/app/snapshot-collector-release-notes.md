---
title: 'Application Insights: Versionshinweise für das NuGet-Paket „Microsoft.ApplicationInsights.SnapshotCollector“'
description: In diesem Artikel finden Sie Versionshinweise für das NuGet-Paket „Microsoft.ApplicationInsights.SnapshotCollector“, das vom Application Insights-Momentaufnahmedebugger verwendet wird.
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 11/10/2020
ms.openlocfilehash: 73fea1e1928cf4e1bd5342aa0a4c885ccb5cf137
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548170"
---
# <a name="release-notes-for-microsoftapplicationinsightssnapshotcollector"></a>Versionshinweise zu „Microsoft.ApplicationInsights.SnapshotCollector“

Dieser Artikel enthält Versionshinweise für das NuGet-Paket „Microsoft.ApplicationInsights.SnapshotCollector“ für .NET-Anwendungen, das vom Application Insights-Momentaufnahmedebugger verwendet wird.

[Erfahren Sie mehr](./snapshot-debugger.md) über den Application Insights-Momentaufnahmedebugger für .NET-Anwendungen.

Zum Einreichen von Fehlerberichten und Feedback können Sie unter https://github.com/microsoft/ApplicationInsights-dotnet ein Issue auf GitHub erstellen.

## <a name="release-notes"></a>Versionshinweise

## <a name="1374"></a>[1.3.7.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.4)
Ein Release einer Nebenversion zur Behandlung eines Problems, das beim Testen des Azure App Service-Szenarios zum Anfügen ohne Code erkannt wurde.
### <a name="changes"></a>Änderungen
- Das Ziel „netcoreapp3.0“ hängt jetzt von „Microsoft.ApplicationInsights.AspNetCore >= 2.1.1“ ab (nicht mehr von „>= 2.1.2“).

## <a name="1373"></a>[1.3.7.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.3)
Hierbei handelt es sich um ein Release einer Nebenversion zum Beheben einiger Probleme mit hohen Auswirkungen.
### <a name="bug-fixes"></a>Behebung von Programmfehlern
- Die PDB-Ermittlung im Ordner „wwwroot/bin“ wurde behoben, die nach der Änderung des Suchalgorithmus für Symbole in Version 1.3.6 beschädigt war.
- Die Ungenauigkeit der Ausnahme „ExtractWasCalledMultipleTimesException“ in den Telemetriedaten wurde behoben.

## <a name="137"></a>[1.3.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7)
### <a name="changes"></a>Änderungen
- Das netcoreapp2.0-Ziel von SnapshotCollector hängt (wieder) von Microsoft.ApplicationInsights.AspNetCore >= 2.1.1 ab. Damit wurde das Verhalten wiederhergestellt, das vor Version 1.3.5 vorhanden war. In Version 1.3.6 wurde ein Upgrade versucht, das jedoch zu Problemen bei einigen Azure App Service-Szenarios führte.
### <a name="new-features"></a>Neue Funktionen
- Snapshot Collector liest und analysiert den ConnectionString-Wert aus der Umgebungsvariable APPLICATIONINSIGHTS_CONNECTION_STRING oder aus TelemetryConfiguration. Dies wird hauptsächlich zum Festlegen des Endpunkts zum Herstellen einer Verbindung mit dem Momentaufnahmedienst verwendet. Weitere Informationen finden Sie in der [Dokumentation zu Verbindungszeichenfolgen](./sdk-connection-string.md).
### <a name="bug-fixes"></a>Behebung von Programmfehlern
- Für alle Ziele außer „net45“ wurde zur Verwendung von HttpClient gewechselt, da WebRequest in einigen Umgebungen aufgrund eines inkompatiblen SecurityProtocol fehlgeschlagen ist (erfordert TLS 1.2).

## <a name="136"></a>[1.3.6](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.6)
### <a name="changes"></a>Änderungen
- SnapshotCollector nutzt nun Microsoft.ApplicationInsights >= 2.5.1 für alle Zielframeworks. Hierbei kann es sich um einen Breaking Change handeln, wenn Ihre Anwendung von einer älteren Version des Microsoft.ApplicationInsights SDK abhängig ist.
- Die Unterstützung für TLS 1.0 und 1.1 wurde im Snapshot Uploader entfernt.
- Die Dauer von PDB-Scans weist nun standardmäßig 24 Stunden anstelle von 15 Minuten auf. Dies kann über PdbRescanInterval in SnapshotCollectorConfiguration konfiguriert werden.
- PDB-Scans durchsucht nur die Ordner der obersten Ebene, anstatt rekursiv zu suchen. Hierbei kann es sich um ein Breaking Change handeln, wenn sich Ihre Symbole in Unterordnern des Binärordners befinden.
### <a name="new-features"></a>Neue Funktionen
- Die Protokollrotation wurde in SnapshotUploader zum Vermeiden des Auffüllen der Protokollordner mit alten Dateien hinzugefügt.
- Die Unterstützung der Deoptimierung (über ReJIT bei Anfügung) für .NET Core 3.0-Anwendungen wurde hinzugefügt.
- Symbole wurden zu NuGet-Paketen hinzugefügt.
- Es wurden zusätzliche Metadaten beim Upload von Minidumps festgelegt.
- Eine Initialized-Eigenschaft wurde zu SnapshotCollectorTelemetryProcessor hinzugefügt. Dabei handelt es sich um ein CancellationToken, das abgebrochen wird, wenn Snapshot Collector vollständig initialisiert und mit dem Dienstendpunkt verbunden ist.
- Momentaufnahmen können nun für Ausnahmen in dynamisch generierten Methoden erfasst werden. Zum Beispiel für die kompilierten Ausdrucksbaumstrukturen, die von Entity Framework-Abfragen generiert werden.
### <a name="bug-fixes"></a>Behebung von Programmfehlern
- Die Ausnahme „AmbiguousMatchException“ wurde behoben, die aufgrund des Statusmonitors beim Laden von Snapshot Collector ausgelöst wurde.
- Die GetSnapshotCollector-Erweiterungsmethode durchsucht nun alle Instanzen von TelemetrySinks.
- Snapshot Uploader wird nicht auf nicht unterstützten Plattformen gestartet.
- InvalidOperationException wird bei der Deoptimierung dynamischer Methoden behandelt (z. B. Entity Framework)

## <a name="135"></a>[1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5)
- Unterstützung für Sovereign Clouds wurde hinzugefügt (ältere Versionen funktionieren in Sovereign Clouds nicht)
- Die Verwendung von AddSnapshotCollector() vereinfacht das Hinzufügen von Snapshot Collector. Weitere Informationen finden Sie [hier](./snapshot-debugger-appservice.md).
- Die FISMA MD5-Einstellung wird zum Verifizieren von Blobblöcken verwendet. Dadurch wird der .NET MD5-Standardkryptografiealgorithmus vermieden, der nicht verfügbar ist, wenn der FIPS-kompatible Modus für das Betriebssystem festgelegt wird.
- Frames von .NET Framework werden beim Deoptimieren von Funktionsaufrufen ignoriert. Dieses Verhalten kann mithilfe der Konfigurationseinstellung „DeoptimizeIgnoredModules“ gesteuert werden.
- Die Konfigurationseinstellung `DeoptimizeMethodCount` wurde hinzugefügt, die die Deoptimierung von mehr als nur einem Funktionsaufruf ermöglicht. Weitere Informationen finden Sie hier.

## <a name="134"></a>[1.3.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.4)
- Strukturierte Instrumentierungsschlüssel werden zugelassen.
- Die Stabilität von SnapshotUploader wurde verbessert. Der Start wird fortgesetzt, selbst wenn die Uploaderprotokolle nicht verschoben werden können.
- Die Berichterstellung für zusätzliche Telemetrie, wenn „SnapshotUploader.exe“ sofort beendet wird, wurde wieder aktiviert (dieses Feature wurde in Version 1.3.3 deaktiviert).
- Die interne Telemetrie wurde vereinfacht.
- _Experimentelles Feature:_ Pläne für Andockpunktsammlungen: „snapshotOnFirstOccurence“ wurde hinzugefügt. Weitere Informationen dazu finden Sie [unter diesem Link](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe).

## <a name="133"></a>[1.3.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.3)
- Ein Fehler wurde behoben, durch den „SnapshotUploader.exe“ nicht mehr reagiert und keine Momentaufnahmen für .NET Core-Apps hochgeladen hat.

## <a name="132"></a>[1.3.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.2)
- _Experimentelles Feature:_ Pläne für Andockpunktsammlungen. Weitere Informationen dazu finden Sie [unter diesem Link](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe).
- „SnapshotUploader.exe“ wird beendet, wenn die Runtime die AppDomain (Anwendungsdomäne) entlädt, aus der SnapshotCollector geladen wird, anstatt dass auf die Beendigung des Prozesses gewartet wird. Dies verbessert die Zuverlässigkeit des Collectors, wenn dieser in IIS gehostet wird.
- Es wurde Konfiguration hinzugefügt, die mehrere SnapshotCollector-Instanzen zulässt, die denselben Instrumentierungsschlüssel verwenden, um den gleichen SnapshotUploader-Prozess zu verwenden: ShareUploaderProcess (Standardwert: `true`).
- Zusätzliche Telemetriedaten werden gemeldet, wenn „SnapshotUploader.exe“ sofort beendet wird.
- Die Anzahl der Unterstützungsdateien, die „SnapshotUploader.exe“ auf den Datenträger schreiben muss, wurde reduziert.

## <a name="131"></a>[1.3.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.1)
- Die Unterstützung für das Erfassen von Momentaufnahmen mit der RtlCloneUserProcess-API wurde entfernt. Nur die PssCaptureSnapshots-API wird unterstützt.
- Das Standardlimit für die maximale Anzahl von Momentaufnahmen, die innerhalb von 10 Minuten erfasst werden, wurde von 1 auf 3 erhöht.
- Für „SnapshotUploader.exe“ wurde die Aushandlung von TLS 1.1 und 1.2 zugelassen.
- Zusätzliche Telemetriedaten werden gemeldet, wenn SnapshotUploader eine Warnung oder einen Fehler protokolliert.
- Das Erstellen von Momentaufnahmen wird beendet, wenn der Back-End-Dienst meldet, dass das tägliche Kontingent erreicht wurde (50 Momentaufnahmen pro Tag).
- Es wurde eine zusätzliche Überprüfung zu „SnapshotUploader.exe“ hinzugefügt, um die gleichzeitige Ausführung von zwei Instanzen zu untersagen.

## <a name="130"></a>[1.3.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.0)
### <a name="changes"></a>Änderungen
- Bei Anwendungen für .NET Framework hängt Snapshot Collector nun von Microsoft.ApplicationInsights Version 2.3.0 oder höher ab.
Zuvor wurde Version 2.2.0 oder höher verwendet.
Dies sollte für die meisten Anwendungen kein Problem darstellen, teilen Sie es jedoch Microsoft mit, wenn diese Änderung Sie an der Verwendung der neuesten Version von Snapshot Collector hindert.
- Beim Wiederholen fehlgeschlagener Uploads werden exponentielle Backoffverzögerungen im Snapshot Uploader verwendet.
- ServerTelemetryChannel wird für zuverlässigere Berichterstattung von Telemetriedaten verwendet (sofern verfügbar).
- SdkInternalOperationsMonitor wird bei der ersten Herstellung der Verbindung mit dem Momentaufnahmedebugger-Dienst verwendet, damit diese von der Abhängigkeitsüberwachung ignoriert wird.
- Die Telemetrie bei der ersten Verbindung mit dem Momentaufnahmedebugger-Dienst wurde verbessert.
- Es werden zusätzliche Telemetriedaten für Folgendes gemeldet:
  - Azure App Service-Version
  - Compute-Instanzen von Azure
  - Container.
  - Azure Functions-Apps
### <a name="bug-fixes"></a>Behebung von Programmfehlern
- Wenn das Zurücksetzungsintervall für den Problemzähler auf 24 Tage festgelegt ist, wird dies als 24 Stunden interpretiert.
- Ein Fehler wurde behoben, bei dem der Snapshot Uploader die Verarbeitung neuer Momentaufnahmen beendet hat, wenn eine Ausnahme beim Löschen einer Momentaufnahme aufgetreten ist.

## <a name="123"></a>[1.2.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.3)
- Das Signieren mit starkem Namen mithilfe von Snapshot Uploader-Binärdateien wurde behoben.

## <a name="122"></a>[1.2.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.2)
### <a name="changes"></a>Änderungen
- Die für „SnapshotUploader(64).exe“ erforderlichen Dateien sind nun als Ressourcen in die Haupt-DLL eingebettet. Das bedeutet, dass der Ordner „SnapshotCollectorFiles“ nicht mehr erstellt wird, wodurch die Erstellung und Bereitstellung vereinfacht und die Übersichtlichkeit im Projektmappen-Explorer verbessert wird. Überprüfen Sie die Änderungen an Ihrer `.csproj`-Datei sorgfältig, wenn Sie ein Upgrade durchführen. Die `Microsoft.ApplicationInsights.SnapshotCollector.targets`-Datei ist nicht mehr erforderlich.
- Telemetriedaten werden in Ihrer Application Insights-Ressource protokolliert, selbst wenn ProvideAnonymousTelemetry auf FALSE festgelegt ist. Dadurch kann ein Feature für die Integritätsprüfung im Azure-Portal implementiert werden. ProvideAnonymousTelemetry wirkt sich nur auf die Telemetriedaten aus, die für Produktsupport und -verbesserung an Microsoft gesendet werden.
- Wenn TempFolder oder ShadowCopyFolder an Umgebungsvariablen weitergeleitet werden, sollte der Collector im Leerlauf gehalten werden, bis die Umgebungsvariablen festgelegt wurden.
- Für Anwendungen, die über einen Proxyserver eine Verbindung mit dem Internet herstellen, erkennt Snapshot Collector Proxyeinstellungen automatisch und übergibt diese an „SnapshotUploader.exe“.
- Die Priorität des SnapshotUploader-Prozesses wurde (nach Möglichkeit) gesenkt. Diese Priorität kann mithilfe der IsLowPrioirtySnapshotUploader-Option überschrieben werden.
- Eine GetSnapshotCollector-Erweiterungsmethode wurde in TelemetryConfiguration für Szenarios hinzugefügt, in denen Sie Snapshot Collector programmgesteuert konfigurieren müssen.
- In für Kunden sichtbare Telemetriedaten wurde die Version des Application Insights SDK (anstelle der Anwendungsversion) angegeben.
- Das erste Heartbeatereignis wird nach zwei Minuten gesendet.
### <a name="bug-fixes"></a>Behebung von Programmfehlern
- Die Ausnahme „NullReferenceException“ wurde behoben, die aufgetreten ist, wenn Ausnahmen Datenwörterbücher aufwiesen, die NULL oder unveränderlich waren.
- Der PDB-Abgleich im Uploader wird mehrmals wiederholt, wenn eine Freigabeverletzung auftritt.
- Die doppelten Telemetriedaten bei mehreren Threadaufrufen in die Telemetriepipeline beim Start wurden behoben.

## <a name="121"></a>[1.2.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.1)
### <a name="changes"></a>Änderungen
- XML-Dokumentationskommentardateien sind nun im NuGet-Paket enthalten.
- Eine ExcludeFromSnapshotting-Erweiterungsmethode für `System.Exception` wurde für Szenarios hinzugefügt, bei denen Sie wissen, dass Sie über eine ungenaue Ausnahme verfügen, und das Erstellen von Momentaufnahmen für diese vermeiden möchten.
- Die Konfigurationseigenschaft „IsEnabledWhenProfiling“ mit dem Standardwert TRUE wurde hinzugefügt. Hierbei handelt es sich um eine Änderung der vorherigen Versionen, bei denen die Momentaufnahmenerstellung vorübergehend deaktiviert wurde, wenn Application Insights Profiler eine ausführliche Sammlung durchgeführt hat. Das alte Verhalten kann wiederhergestellt werden, indem diese Eigenschaft auf FALSE festgelegt wird.
### <a name="bug-fixes"></a>Behebung von Programmfehlern
- „SnapshotUploader64.exe“ wird nun ordnungsgemäß signiert.
- Der Schutz vor doppelter Initialisierung des Telemetrieprozessors wurde eingerichtet.
- Die doppelte Protokollierung von Telemetriedaten in Apps mit mehreren Pipelines wird nun verhindert.
- Ein Fehler beim Ablaufdatum von Sammlungsplänen wurde behoben, der Momentaufnahmen nach 24 Stunden verhindern konnte.

## <a name="120"></a>[1.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.0)
Die größte Änderung dieser Version ist eine Umschreibung der Erstellung von Momentaufnahmen und der Verarbeitungspipeline (daher der Wechsel zu einer neuen Nebenversionsnummer). In vorherigen Versionen wurde diese Funktionalität in nativem Code implementiert („ProductionBreakpoints *.dll“ und „SnapshotHolder*.exe“). Bei der neuen Implementierung handelt es sich ausschließlich um verwalteten Code mit P/Invokes (Plattformaufrufen). Für diese erste Version mit der neuen Pipeline wurde das ursprüngliche Verhalten kaum geändert. Die neue Implementierung ermöglicht bessere Fehlerberichterstellung und bereitet den Weg für zukünftige Verbesserungen.

### <a name="other-changes-in-this-version"></a>Andere Änderungen in dieser Version
- „MinidumpUploader.exe“ wurde in „SnapshotUploader.exe“ (oder „SnapshotUploader64.exe“) umbenannt.
- Es wurden Zeittelemetriedaten zu DeOptimize-/ReOptimize-Anforderungen hinzugefügt.
- Die GZIP-Komprimierung wurde für Uploads von Minidumps hinzugefügt.
- Ein Problem wurde behoben, bei dem PDB-Dateien gesperrt waren, wodurch Standortupgrades verhindert wurden.
- Der ursprüngliche Ordnername (SnapshotCollectorFiles) wird nun beim Erstellen von Schattenkopien protokolliert.
- Die Arbeitsspeicherlimits für 64-Bit-Prozesse wurden angepasst, um Standortneustarts aufgrund von unzureichendem Arbeitsspeicher zu verhindern.
- Ein Fehler wurde behoben, bei dem Momentaufnahmen auch nach der Deaktivierung weiterhin erfasst wurden.
- Heartbeatereignisse werden nun in der KI-Ressource des Kunden protokolliert.
- Die Geschwindigkeit der Momentaufnahmenerstellung wurde erhöht, indem „Source“ (Quelle) aus der Problem-ID entfernt wurde.

## <a name="112"></a>[1.1.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.2)
### <a name="changes"></a>Änderungen
Erweiterte Nutzungstelemetrie
- Die .NET-Version und das Betriebssystem werden erkannt und gemeldet.
- Zusätzliche Azure-Umgebungen (z. B. Clouddienst und Service Fabric) werden erkannt und gemeldet.
- Ausnahmemetriken (die Anzahl der Ausnahmen bei erster Möglichkeit und die Anzahl von TrackException-Aufrufen) werden in den Heartbeattelemetriedaten aufgezeichnet und gemeldet.
### <a name="bug-fixes"></a>Behebung von Programmfehlern
- Richtige Verarbeitung von SqlException, bei der die innere Ausnahme (Win32Exception) nicht ausgelöst wird
- Die nachstehenden Leerzeichen in Symbolordnern wurden gekürzt, was zu einer fehlerhaften Analyse von Befehlszeilenargumenten für MinidumpUploader führte.
- Das unbegrenzte Wiederholen von fehlgeschlagenen Verbindungen mit dem Endpunkt des Momentaufnahmedebugger-Agents wird nun verhindert.

## <a name="110"></a>[1.1.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.0)
### <a name="changes"></a>Änderungen
- Es wurde ein Schutzmechanismus für den Hostarbeitsspeicher hinzugefügt. Dieses Feature reduziert die Auswirkungen auf den Arbeitsspeicher des Hostcomputers.
- Die Anzeige der Momentaufnahmen im Azure-Portal wurde verbessert.