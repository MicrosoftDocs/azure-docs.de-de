---
title: Versionshinweise für Azure SQL Edge
description: Versionshinweise mit Informationen zu Neuerungen oder Änderungen in den Azure SQL Edge-Images
keywords: Versionshinweise für SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 11/24/2020
ms.openlocfilehash: bb0c176ee8c145e1268827d79b453628e6f1bc28
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95808628"
---
# <a name="azure-sql-edge-release-notes"></a>Azure SQL Edge: Versionshinweise 

In diesem Artikel erhalten Sie Informationen zu Neuerungen und Änderungen für neue Azure SQL Edge-Builds.

## <a name="azure-sql-edge---101"></a>Azure SQL Edge – 1.0.1

### <a name="sql-engine-build-number---15020001553"></a>SQL-Engine-Buildnummer – 15.0.2000.1553

### <a name="whats-new"></a>Neuigkeiten

- In berechneten Spalten definierter Datebucket-Ausdruck wird zugelassen.

### <a name="fixes"></a>Fehlerbehebungen

- Korrektur der Aufbewahrungsrichtlinie für das Löschen einer Tabelle mit aktivierter Aufbewahrungsrichtlinie mit unbegrenztem Timeout
- Unterstützung von Streaming- und Aufbewahrungsrichtlinienfeatures durch die DacFx-Bereitstellung 
- Korrektur der DacFx-Bereitstellung zum Aktivieren der Bereitstellung aus einem geschachtelten Ordner in der SAS-URL 
- PREDICT-Korrektur zur Unterstützung langer Spaltennamen in Fehlermeldungen

## <a name="azure-sql-edge---100-rtm"></a>Azure SQL Edge – 1.0.0 (RTM)

### <a name="sql-engine-build-number---15020001552"></a>SQL-Engine-Buildnummer – 15.0.2000.1552

### <a name="whats-new"></a>Neuigkeiten
1. Auf Ubuntu 18.04 basierende Containerimages. 
2. Unterstützung für `IGNORE NULL`- und `RESPECT NULL`-Syntax mit `LAST_VALUE()`- und `FIRST_VALUE()`-Funktionen. 
3. Verbesserungen der Zuverlässigkeit für PREDICT mit ONNX.
4. Unterstützung für auf der Datenaufbewahrungsrichtlinie basierende Bereinigung.
   - Ringpufferunterstützung für die Aufgabe „Bereinigung der Datenaufbewahrung“ zur Problembehandlung.
5. Unterstützung für neue Funktionen 
   - Schnelle Wiederherstellung
   - Automatische Optimierung von Abfragen
   - Aktivieren von parallelen Ausführungsszenarien
6. Verbesserungen der Energiesparfunktion bei niedrigem Energiestatus
7. Streaming der Unterstützung für neue Funktionen 
   - [Momentaufnahmefenster](/stream-analytics-query/snapshot-window-azure-stream-analytics): neuer Fenstertyp, der das Gruppieren nach gleichzeitig eintreffenden Ereignissen ermöglicht. 
   - Aktivieren Sie [TopOne](/stream-analytics-query/topone-azure-stream-analytics) und [CollectTop](/stream-analytics-query/collecttop-azure-stream-analytics) als analytische Funktion. Dadurch können (nach der Spalte Ihrer Wahl sortierte) Datensätze zurückgegeben werden, ohne dass sie Teil eines Fensters sein müssen. 
   - Verbesserungen bei [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics). 

### <a name="fixes"></a>Fehlerbehebungen
1. Zusätzliche Fehlermeldungen und Details zur Problembehandlung von TQL-Streamingvorgängen. 
2. Verbesserungen, um die Akkulebensdauer im Leerlaufmodus zu verlängern. 
3. Fehlerbehebungen für TQL-Streamingmodule: 
   - Bereinigung für beendeten Streamingauftrag 
   - Korrekturen für Lokalisierung und Verbesserungen bei der Unicode-Verarbeitung
   - Verbesserte Debugfähigkeit bei Edge-TSQL-Streaming ermöglicht Benutzern das Abfragen von Auftragsfehlern aus „get_streaming_job“.
4. Auf der Datenaufbewahrungsrichtlinie basierende Bereinigung
   - Korrekturen für die Erstellung von Aufbewahrungsrichtlinien und Bereinigungsszenarien.
5. Korrekturen für Timertasks im Hintergrund, um Energieeinsparungen für den Energiesparmodus zu verbessern.

### <a name="known-issues"></a>Bekannte Probleme 
1. T-SQL-Funktion „Date_Bucket“ kann in einer berechneten Spalte nicht verwendet werden.


## <a name="ctp-23"></a>CTP 2.3
### <a name="sql-engine-build-number---15020001549"></a>SQL-Engine-Buildnummer – 15.0.2000.1549
### <a name="whats-new"></a>Neuigkeiten
1. Unterstützung für benutzerdefinierte Ursprünge in der Date_Bucket()-Funktion. 
2. Unterstützung für BacPac-Dateien als Teil der SQL-Bereitstellung.
3. Unterstützung für auf der Datenaufbewahrungsrichtlinie basierende Bereinigung.      
   - DDL-Unterstützung für das Aktivieren der Aufbewahrungsrichtlinie 
   - Gespeicherte Bereinigungsprozeduren und Bereinigungstask im Hintergrund
   - Erweiterte Ereignisse zum Überwachen von Bereinigungstasks

### <a name="fixes"></a>Fehlerbehebungen
1. Zusätzliche Fehlermeldungen und Details zur Problembehandlung von TQL-Streamingvorgängen. 
2. Verbesserungen, um die Akkulebensdauer im Leerlaufmodus zu verlängern. 
3. Fehlerbehebungen für TQL-Streamingmodule: 
   - Korrektur hängendes Wasserzeichen mit springendem Substreamfenster 
   - Korrektur der Frameworkausnahmebehandlung, um sicherzustellen, dass die Erfassung als Fehler mit möglicher Benutzeraktion erfolgt


## <a name="ctp-22"></a>CTP 2.2
### <a name="sql-engine-build-number---15020001546"></a>SQL-Engine-Buildnummer: 15.0.2000.1546
### <a name="whats-new"></a>Neuigkeiten
1. Unterstützung für Container ohne Root-Berechtigung 
2. Unterstützung für Verbrauchs- und Diagnosedatensammlung 
3. T-SQL-Streamingupdates
   - Unterstützung für Unicode-Zeichen für Streamobjektnamen

### <a name="fixes"></a>Fehlerbehebungen
1. T-SQL-Streamingupdates
   - Verbesserungen der Prozessbereinigung
   - Verbesserungen bei Protokollierung und Diagnose
2. Leistungsverbesserung bei der Datenerfassung

## <a name="ctp-21"></a>CTP 2.1 
### <a name="sql-engine-build-number---15020001545"></a>SQL-Engine-Buildnummer: 15.0.2000.1545
### <a name="fixes"></a>Fehlerbehebungen
1. Vorhersage (PREDICT) mit ONNX-Modellen korrigiert, um das CPUID-Problem in ARM zu behandeln 
2. Behandlung des Fehlerpfads beim Starten des TQL-Streamings verbessert 
3. Falscher Wert der Wasserzeichenverzögerung in Auftragsmetriken korrigiert, wenn keine Daten vorhanden sind 
4. Problem mit dem Ausgabeadapter behoben, wenn der Adapter über ein variables Schema zwischen Batches verfügt  

## <a name="ctp-20"></a>CTP 2.0 
### <a name="sql-engine-build-number---15020001401"></a>SQL-Engine-Buildnummer: 15.0.2000.1401
### <a name="whats-new"></a>Neuigkeiten
1.  Produktname wurde in „Azure SQL Edge“ geändert
1.  Funktion „Date_bucket“

    i.  Unterstützung der Typen „Date“, „Time“ und „DateTime“
3.  Vorhersage (PREDICT) mit ONNX
    
    i.  Parameter „RUNTIME“ für ONNX erforderlich 
    
4.  Unterstützung von TSQL-Streaming (eingeschränkte Vorschau) 
 
### <a name="known-issues"></a>Bekannte Probleme

1. <b>Problem:</b> Mögliche Fehler beim Anwenden von DACPAC beim Start aufgrund eines Zeitsteuerungsproblems.

    <b>Problemumgehung:</b> Wenn Sie SQL Server oder den Container neu starten, wird erneut versucht, DACPAC anzuwenden, und das Problem sollte behoben werden.
### <a name="request-support"></a>Anfordern von Support
1. Support kann auf der [Supportseite](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) angefordert werden.

4. Folgende Felder müssen ausgewählt sein: 
    * Problemtyp: Technisch 
    * Dienst: IoT Edge
    * Problemtyp: Mein Problem bezieht sich auf ein IoT Edge-Modul
    * Problemuntertyp: Azure SQL Edge

   ![Beispiel für in Supportticket](media/get-support/support-ticket.png)

## <a name="ctp-15"></a>CTP 1.5
### <a name="sql-engine-build-number---15020001331"></a>SQL-Engine-Buildnummer: 15.0.2000.1331
### <a name="whats-new"></a>Neuigkeiten
1. Funktion „Date_bucket“
    
    i. Unterstützung des Typs „DateTimeOffset“
2. Vorhersage (PREDICT) mit ONNX-Modellen

    i. nvarchar-Unterstützung
 
## <a name="ctp-14"></a>CTP 1.4
### <a name="sql-engine-build-number---15020001247"></a>SQL-Engine-Buildnummer: 15.0.2000.1247
### <a name="whats-new"></a>Neuigkeiten
1.  Vorhersage (PREDICT) mit ONNX-Modellen
 
    i.  varchar-Unterstützung
    
    ii. Migration zur ONNX-Runtimeversion 1.0 
2.  Featureunterstützung: Folgende Features sind aktiviert:

    i.   CDC-Unterstützung

    ii.  Verlaufstabelle mit Komprimierung

    iii. Höherer Skalierungsfaktor für Protokoll-Read-Ahead

    iv.  ES-Filterweitergabe für Batchmodus

    v.   Read-Ahead-Optimierungen
 
## <a name="ctp-13"></a>CTP 1.3
### <a name="sql-engine-build-number---15020001147"></a>SQL-Engine-Buildnummer: 15.0.2000.1147
### <a name="whats-new"></a>Neuigkeiten
1. Azure IoT-Portalbereitstellung 

    i.   Unterstützung der Bereitstellung von AMD64- und ARM-Images

    ii.  Unterstützung der Streamingauftragserstellung

    iii. DACPAC-Bereitstellung
2. Vorhersage (PREDICT) mit ONNX-Modellen

    i. Unterstützung numerischer Typen
3. Featureunterstützung: Folgende Features sind aktiviert:

    i.  Weitergabe von Aggregat an Columnstore-Scan

    ii. Karussellscans
4. Verringerung des Speicher- und Arbeitsspeicherbedarfs
