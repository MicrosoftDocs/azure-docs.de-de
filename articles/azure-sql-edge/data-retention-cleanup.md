---
title: 'Verwalten von historischen Daten mit einer Aufbewahrungsrichtlinie: Azure SQL Edge'
description: Erfahren Sie, wie historische Daten mit einer Aufbewahrungsrichtlinie in Azure SQL Edge verwaltet werden können.
keywords: SQL Edge, Datenaufbewahrung
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: f547f0b3219889b54364c4805c2dd5b183a9861a
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392366"
---
# <a name="manage-historical-data-with-retention-policy"></a>Verwalten von historischen Daten mit einer Aufbewahrungsrichtlinie

Datenaufbewahrung kann für die Datenbank und jede der zugrunde liegenden Tabellen einzeln aktiviert werden, sodass Benutzer flexible Alterungsrichtlinien für ihre Tabellen und Datenbanken erstellen können. Das Anwenden von Datenaufbewahrung ist einfach: Sie erfordert nur einen Parameter, der bei der Tabellenerstellung oder als Bestandteil eines ALTER TABLE-Vorgangs festgelegt werden muss. 

Nachdem Datenaufbewahrungsrichtlinien für eine Datenbank und die zugrunde liegenden Tabellen definiert wurden, wird eine Hintergrundaufgabe ausgeführt, um alle veralteten Datensätze aus den Tabellen zu entfernen, die für Datenaufbewahrung aktiviert sind. Die Ermittlung übereinstimmender Zeilen und deren Entfernung aus der Tabelle erfolgen transparent mithilfe einer vom System geplanten und ausgeführten Hintergrundaufgabe. Die Altersbedingung für die Tabellenzeilen wird anhand der Spalte geprüft, die als `filter_column`-Spalte in der Tabellendefinition verwendet wird. Wenn der Aufbewahrungszeitraum beispielsweise auf eine Woche festgelegt ist, erfüllen die für die Bereinigung infrage kommenden Zeilen eine der folgenden Bedingungen: 

- Hat die Filterspalte den Datentyp DATETIMEOFFSET, sieht die Bedingung so aus: `filter_column < DATEADD(WEEK, -1, SYSUTCDATETIME())`.
- Andernfalls sieht die Bedingung so aus: `filter_column < DATEADD(WEEK, -1, SYSDATETIME())`.

## <a name="data-retention-cleanup-phases"></a>Phasen der Bereinigung für eine Datenaufbewahrung

Der Bereinigungsvorgang für eine Datenaufbewahrung besteht aus zwei Phasen. 
- Ermittlungsphase: In dieser Phase werden im Bereinigungsvorgang alle Tabellen in den Benutzerdatenbanken ermittelt, um eine Liste für die Bereinigung zu erstellen. Diese Ermittlung wird einmal täglich ausgeführt.
- Bereinigungsphase: In dieser Phase wird die Bereinigung für alle Tabellen mit begrenzter Datenaufbewahrung ausgeführt, die in der Ermittlungsphase ermittelt wurden. Kann der Bereinigungsvorgang für eine Tabelle nicht ausgeführt werden, wird diese Tabelle in der aktuellen Ausführung übersprungen, und in der nächsten Iteration wird versucht, den Vorgang zu wiederholen. Die folgenden Prinzipien werden während einer Bereinigung verwendet:
    - Wenn eine veraltete Zeile durch eine andere Transaktion gesperrt ist, wird diese Zeile übersprungen. 
    - Bereinigen wird mit einer Standardeinstellung von 5 Sekunden für ein Sperrtimeout ausgeführt. Können die Sperren für eine Tabelle nicht innerhalb des Timeoutfensters aufgehoben werden, wird die Tabelle bei der aktuellen Ausführung übersprungen, und in der nächsten Iteration wird versucht, den Vorgang zu wiederholen.
    - Tritt während der Bereinigung einer Tabelle ein Fehler auf, wird diese Tabelle übersprungen und in der nächsten Iteration erneut berücksichtigt.

## <a name="manual-cleanup"></a>Manuelle Bereinigung

Abhängig von den Datenaufbewahrungseinstellungen für eine Tabelle und der Art des Workloads für die Datenbank kann es passieren, dass der automatische Bereinigungsthread bei seiner Ausführung nicht alle veralteten Zeilen entfernt. Um Unterstützung hierfür zu bieten und Benutzern das manuelle Entfernen veralteter Zeilen zu ermöglichen, wurde die gespeicherte Prozedur `sys.sp_cleanup_data_retention` in Azure SQL Edge eingeführt. 

Diese gespeicherte Prozedur hat drei Parameter. 
    - Schemaname: der Name des besitzenden Schemas für die Tabelle. Dies ist ein erforderlicher Parameter. 
    - Tabellenname: der Name der Tabelle, für die die manuelle Bereinigung ausgeführt wird. Dies ist ein erforderlicher Parameter. 
    - Zeilenanzahl (rowcnt, output): Ausgabevariable. Gibt die Anzahl der Zeilen zurück, die bei der manuellen Bereinigung bereinigt wurden. Dies ist ein optionaler Parameter. 

Das folgende Beispiel zeigt, wie die gespeicherte Prozedur zur manuellen Bereinigung der Tabelle `dbo.data_retention_table` ausgeführt wird.

```sql
declare @rowcnt bigint 
EXEC sys.sp_cleanup_data_retention 'dbo', 'data_retention_table', @rowcnt output 
select @rowcnt 
```

## <a name="how-obsolete-rows-are-deleted"></a>Löschen veralteter Zeilen

Der Bereinigungsprozess hängt vom Indexlayout der Tabelle ab. Es wird eine Hintergrundaufgabe erstellt, um die Bereinigung veralteter Daten für alle Tabellen mit begrenztem Aufbewahrungszeitraum auszuführen. Die Bereinigungslogik für den Rowstore-Index (B-Struktur oder Heap) löscht veraltete Zeilen in kleineren Blöcken (max. 10.000), was die Belastung des Datenbankprotokolls und des E/A-Subsystems minimiert. Die Bereinigungslogik verwendet zwar den benötigten B-Strukturindex, jedoch kann die Löschreihenfolge der Zeilen, die den Beibehaltungszeitraum übersteigen, nicht sicher garantiert werden. Verwenden Sie in Ihren Anwendungen daher keine Abhängigkeit von der Bereinigungsreihenfolge.

Die Bereinigungsaufgabe für den gruppierten Columnstore entfernt ganze Zeilengruppen auf einmal (die üblicherweise jeweils 1 Mio. Reihen enthalten). Dies ist sehr effizient, insbesondere wenn Verlaufsdaten sehr schnell generiert werden und veraltet sind.

![Bereinigung der Datenaufbewahrung](./media/data-retention-cleanup/data-retention-cleanup.png)

Die hervorragende Datenkompression und die effiziente Aufbewahrungsbereinigung machen den gruppierten Columnstore-Index zur perfekten Lösung für Szenarios, bei denen Ihre Workload in kürzester Zeit eine große Menge an Daten generiert.

> [!Note]
> Im Fall von B-Strukturindizes und Heaps wird für eine Datenaufbewahrung eine Löschabfrage für die zugrunde liegenden Tabellen ausgeführt, die in Konflikt mit den Löschtriggern für die Tabellen stehen kann. Es wird empfohlen, entweder die Löschtrigger für die Tabellen zu entfernen oder Datenaufbewahrung nicht für Tabellen zu aktivieren, die Lösch-DML-Trigger haben.

## <a name="monitoring-data-retention-cleanup"></a>Überwachen der Bereinigung der Datenaufbewahrung

Bereinigungsvorgänge einer Datenaufbewahrungsrichtlinie können mithilfe von erweiterten Ereignissen (XEvents) in Azure SQL Edge überwacht werden. Weitere Informationen zu erweiterten Ereignissen finden Sie unter [Übersicht über erweiterte Ereignisse](/sql/relational-databases/extended-events/extended-events). 

Mit den folgenden sechs erweiterten Ereignissen lässt sich der Zustand der Bereinigungsvorgänge verfolgen. 

| name | Beschreibung |
|------| ------------|
| data_retention_task_started  | Tritt auf, wenn eine Hintergrundaufgabe zum Bereinigen von Tabellen mit einer Aufbewahrungsrichtlinie gestartet wird. |
| data_retention_task_completed  | Tritt auf, wenn eine Hintergrundaufgabe zum Bereinigen von Tabellen mit einer Aufbewahrungsrichtlinie beendet wird. |
| data_retention_task_exception  | Tritt auf, wenn in einer Hintergrundaufgabe zur Bereinigung von Tabellen mit Aufbewahrungsrichtlinie außerhalb des für eine Tabelle speziellen Aufbewahrungsbereinigungsprozesses ein Fehler ermittelt wird. |
| data_retention_cleanup_started  | Tritt auf, wenn der Bereinigungsprozess für eine Tabelle mit Datenaufbewahrungsrichtlinie gestartet wird. |
| data_retention_cleanup_exception  | Tritt auf, wenn bei dem Bereinigungsprozess für eine Tabelle mit Aufbewahrungsrichtlinie ein Fehler auftritt. |
| data_retention_cleanup_completed  | Tritt auf, wenn der Bereinigungsprozess für eine Tabelle mit Datenaufbewahrungsrichtlinie beendet wird. |  

Außerdem wurde der dynamischen Verwaltungssicht „sys.dm_os_ring_buffers“ ein neuer Ringpuffertyp namens `RING_BUFFER_DATA_RETENTION_CLEANUP` hinzugefügt. Diese Sicht kann dazu verwendet werden, die Bereinigungsvorgänge für eine Datenaufbewahrung zu überwachen. 


## <a name="next-steps"></a>Nächste Schritte
- [Datenaufbewahrungsrichtlinie](data-retention-overview.md)
- [Aktivieren und Deaktivieren von Datenaufbewahrungsrichtlinien](data-retention-enable-disable.md)