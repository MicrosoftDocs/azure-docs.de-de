---
title: Bewährte Methoden für die Azure SQL-Datensynchronisierung
description: Hier können Sie sich über bewährte Methoden beim Konfigurieren und Ausführen der Azure SQL-Datensynchronisierung informieren.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/20/2018
ms.openlocfilehash: 59e28e4a3d630aac0954802e8777058c00261006
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791442"
---
# <a name="best-practices-for-azure-sql-data-sync"></a>Bewährte Methoden für die Azure SQL-Datensynchronisierung 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Dieser Artikel enthält bewährte Methoden für die Azure SQL-Datensynchronisierung.

Eine Übersicht über die SQL-Datensynchronisierung finden Sie unter [Synchronisieren von Daten über mehrere Cloud- und lokale Datenbanken mit SQL-Datensynchronisierung](sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> Die Azure SQL-Datensynchronisierung unterstützt derzeit **nicht** Azure SQL Managed Instance.

## <a name="security-and-reliability"></a><a name="security-and-reliability"></a> Sicherheit und Zuverlässigkeit

### <a name="client-agent"></a>Client-Agent

-   Installieren Sie den Client-Agent, indem Sie das Benutzerkonto mit den geringsten Rechten verwenden, das über Zugriff auf den Netzwerkdienst verfügt.  
-   Installieren Sie den Client-Agent auf einem Computer, bei dem es sich nicht um den SQL Server-Computer handelt.  
-   Registrieren Sie eine lokale Datenbank nicht bei mehreren Agents.    
    -   Vermeiden Sie dies auch, wenn Sie verschiedene Tabellen für unterschiedliche Synchronisierungsgruppen synchronisieren.  
    -   Die Registrierung einer lokalen Datenbank bei mehreren Client-Agents führt zu Problemen, wenn Sie eine der Synchronisierungsgruppen löschen.

### <a name="database-accounts-with-least-required-privileges"></a>Datenbankkonten mit geringstmöglichen Rechten

-   **Für die Einrichtung der Synchronisierung:** Tabelle erstellen/ändern; Datenbank ändern; Prozedur erstellen; Schema auswählen/ändern; benutzerdefinierten Typ erstellen

-   **Für die laufende Synchronisierung:** Auswählen/Einfügen/Aktualisieren/Löschen für Tabellen, die für die Synchronisierung ausgewählt wurden, sowie für Tabellen mit Synchronisierungsmetadaten und für Nachverfolgungstabellen; Ausführungsberechtigung für vom Dienst erstellte gespeicherte Prozeduren; Ausführungsberechtigung für benutzerdefinierte Tabellentypen

-   **Aufhebung der Bereitstellung:** Änderungsberechtigung für an der Synchronisierung beteiligte Tabellen; Auswählen/Löschen für Tabellen mit Synchronisierungsmetadaten; Steuerungsberechtigung für Tabellen zur Synchronisierungsnachverfolgung, gespeicherte Prozeduren und benutzerdefinierte Typen

Azure SQL-Datenbank unterstützt nur einen Satz von Anmeldeinformationen. Sie können die folgenden Optionen verwenden, um diese Aufgaben im Rahmen dieser Einschränkung zu erledigen:

-   Ändern Sie die Anmeldeinformationen für die verschiedenen Phasen. Verwenden Sie also beispielsweise *credentials1* für die Einrichtung und *credentials2* für die laufende Synchronisierung.  
-   Ändern Sie die Berechtigung der Anmeldeinformationen. (Genauer gesagt: Ändern Sie die Berechtigung nach dem Einrichten der Synchronisierung.)

## <a name="setup"></a>Einrichten

### <a name="database-considerations-and-constraints"></a><a name="database-considerations-and-constraints"></a> Überlegungen und Einschränkungen für Datenbanken

#### <a name="database-size"></a>Datenbankgröße

Legen Sie beim Erstellen einer neuen Datenbank die maximale Größe immer auf einen Wert fest, der größer als die bereitgestellte Datenbank ist. Wenn Sie die maximale Größe nicht auf einen höheren Wert als die bereitgestellte Datenbank festlegen, ist die Synchronisierung nicht erfolgreich. Die SQL-Datensynchronisierung ermöglicht zwar keine automatische Vergrößerung, Sie können jedoch den Befehl `ALTER DATABASE` ausführen, um die Größe der Datenbank nach der Erstellung zu erhöhen. Stellen Sie sicher, dass die Größenlimits der Datenbank nicht überschritten werden.

> [!IMPORTANT]
> Die SQL-Datensynchronisierung speichert mit jeder Datenbank zusätzliche Metadaten. Stellen Sie sicher, dass Sie diese Metadaten bei der Berechnung des Speicherbedarfs berücksichtigen. Der zusätzlich benötigte Platz ist abhängig von der Breite der Tabellen (schmale Tabellen benötigen beispielsweise weniger zusätzlichen Platz) sowie vom Umfang des Datenverkehrs.

### <a name="table-considerations-and-constraints"></a><a name="table-considerations-and-constraints"></a> Überlegungen und Einschränkungen für Tabellen

#### <a name="selecting-tables"></a>Auswählen von Tabellen

Sie müssen nicht alle Tabellen, die in einer Datenbank enthalten sind, in eine Synchronisierungsgruppe einfügen. Die Tabellen, die Sie in eine Synchronisierungsgruppe einfügen, haben Einfluss auf die Effizienz und die Kosten. Fügen Sie Tabellen – und die Tabellen, von denen diese abhängig sind – nur dann in eine Synchronisierungsgruppe ein, wenn dies eine geschäftliche Anforderung ist.

#### <a name="primary-keys"></a>Primärschlüssel

Jede Tabelle in einer Synchronisierungsgruppe muss über einen Primärschlüssel verfügen. Tabellen ohne Primärschlüssel können von der SQL-Datensynchronisierung nicht synchronisiert werden.

Testen Sie die anfängliche und fortlaufende Synchronisierungsleistung, bevor Sie die SQL-Datensynchronisierung in der Produktion verwenden.

#### <a name="empty-tables-provide-the-best-performance"></a>Leere Tabellen bieten die beste Leistung

Leere Tabellen bieten zum Zeitpunkt der Initialisierung die beste Leistung. Wenn die Zieltabelle leer ist, verwendet die Datensynchronisierung Masseneinfügevorgänge zum Laden der Daten. Andernfalls vergleicht die Datensynchronisierung die einzelnen Zeilen und fügt Zeilen einzeln ein, um Konflikte zu vermeiden. Wenn die Leistung keine primäre Rolle spielt, können Sie die Synchronisierung zwischen Tabellen einrichten, die bereits Daten enthalten.

### <a name="provisioning-destination-databases"></a><a name="provisioning-destination-databases"></a> Bereitstellen von Zieldatenbanken

Die SQL-Datensynchronisierung bietet eine grundlegende automatische Datenbankbereitstellung.

In diesem Abschnitt werden die Einschränkungen im Zusammenhang mit der Bereitstellung in der SQL-Datensynchronisierung beschrieben.

#### <a name="autoprovisioning-limitations"></a>Einschränkungen der automatischen Bereitstellung

Für die SQL-Datensynchronisierung gelten in Bezug auf die automatische Bereitstellung die folgenden Einschränkungen:

-   Wählen Sie nur die Spalten aus, die in der Zieltabelle erstellt werden. Alle Spalten, die nicht Teil der Synchronisierungsgruppe sind, werden in Zieltabellen bereitgestellt.
-   Indizes werden nur für ausgewählte Spalten erstellt. Enthält der Quelltabellenindex Spalten, die nicht Teil der Synchronisierungsgruppe sind, werden diese Indizes nicht in den Zieltabellen bereitgestellt.  
-   Indizes für XML-Spalten werden nicht bereitgestellt.  
-   CHECK-Einschränkungen werden nicht bereitgestellt.  
-   Vorhandene Trigger für die Quelltabellen werden nicht bereitgestellt.  
-   Sichten und gespeicherte Prozeduren werden in der Zieldatenbank nicht erstellt.
-   Die Aktionen ON UPDATE CASCADE und ON DELETE CASCADE werden für Fremdschlüsseleinschränkungen in den Zieltabellen nicht neu erstellt.
-   Bei dezimalen oder numerischen Spalten mit einer größeren Genauigkeit als 28 kann bei der SQL-Datensynchronisierung während der Synchronisierung ein Konvertierungsüberlaufproblem auftreten. Es wird empfohlen, dass Sie die Genauigkeit von dezimalen oder numerischen Spalten auf 28 oder geringer beschränken.

#### <a name="recommendations"></a>Empfehlungen

-   Verwenden Sie die automatische Bereitstellungsfunktion der SQL-Datensynchronisierung nur während der Testphase des Diensts.  
-   Stellen Sie für eine Produktionsumgebung das Datenbankschema bereit.

### <a name="where-to-locate-the-hub-database"></a><a name="locate-hub"></a> Standort für die Hub-Datenbank

#### <a name="enterprise-to-cloud-scenario"></a>Unternehmen-zu-Cloud-Szenario

Zur Minimierung der Wartezeit sollte sich die Hub-Datenbank in der Nähe der höchsten Datenbank-Datenverkehrskonzentration der Synchronisierungsgruppe befinden.

#### <a name="cloud-to-cloud-scenario"></a>Cloud-zu-Cloud-Szenario

-   Wenn sich alle Datenbanken einer Synchronisierungsgruppe in demselben Datencenter befinden, sollte auch der Hub in diesem Datencenter angeordnet werden. Diese Konfiguration verringert die Wartezeit und die Kosten für Datenübertragungen zwischen Datencentern.
-   Wenn die Datenbanken einer Synchronisierungsgruppe auf mehrere Datencenter verteilt sind, sollte sich der Hub in dem Datencenter befinden, das die Mehrheit der Datenbanken enthält und in dem der meiste Datenbank-Datenverkehr anfällt.

#### <a name="mixed-scenarios"></a>Gemischte Szenarien

Wenden Sie die vorherigen Richtlinien auf komplexe Konfigurationen von Synchronisierungsgruppen an, z.B. bei einer Mischung aus Unternehmen-zu-Cloud- und Cloud-zu-Cloud-Szenarien.

## <a name="sync"></a>Synchronisierung

### <a name="avoid-slow-and-costly-initial-sync"></a><a name="avoid-a-slow-and-costly-initial-synchronization"></a> Vermeiden von langsamen und teuren ersten Synchronisierungsläufen

In diesem Abschnitt wird die erste Synchronisierung einer Synchronisierungsgruppe beschrieben. Sie erfahren, wie Sie verhindern können, dass eine erste Synchronisierung zeitaufwändiger und teurer als nötig wird.

#### <a name="how-initial-sync-works"></a>Ablauf der ersten Synchronisierung

Beginnen Sie beim Erstellen einer Synchronisierungsgruppe mit Daten in einer einzelnen Datenbank. Wenn Sie über Daten in mehreren Datenbanken verfügen, behandelt die SQL-Datensynchronisierung jede Zeile als Konflikt, der gelöst werden muss. Diese Lösung von Konflikten führt dazu, dass die erste Synchronisierung langsam abläuft. Wenn Sie über Daten in mehreren Datenbanken verfügen, kann die erste Synchronisierung je nach Datenbankgröße ggf. zwischen mehreren Tagen und mehreren Monaten dauern.

Falls sich die Datenbanken in unterschiedlichen Datencentern befinden, muss jede Zeile zwischen den einzelnen Datencentern übertragen werden. Dies erhöht die Kosten für eine erste Synchronisierung.

#### <a name="recommendation"></a>Empfehlung

Beginnen Sie möglichst mit Daten in nur einer Datenbank der Synchronisierungsgruppe.

### <a name="design-to-avoid-sync-loops"></a><a name="design-to-avoid-synchronization-loops"></a> Vermeiden von Synchronisierungsschleifen

Zu einer Synchronisierungsschleife kommt es, wenn in einer Synchronisierungsgruppe Zirkelbezüge enthalten sind. Bei diesem Szenario wird jede Änderung in einer Datenbank für die Datenbanken der Synchronisierungsgruppe endlos und in Zirkelform repliziert.   

Achten Sie darauf, Synchronisierungsschleifen zu vermeiden, da sie zu einer Leistungsbeeinträchtigung und ggf. erheblichen Kostensteigerungen führen.

### <a name="changes-that-fail-to-propagate"></a><a name="handling-changes-that-fail-to-propagate"></a> Änderungen, die nicht verteilt werden

#### <a name="reasons-that-changes-fail-to-propagate"></a>Gründe für Fehler bei der Verteilung von Änderungen

Es kann passieren, dass Änderungen aus einem der folgenden Gründe nicht verteilt werden:

-   Inkompatibilität des Schemas/Datentyps
-   Einfügung eines NULL-Werts in Spalten, in denen keine NULL-Werte zulässig sind
-   Verstoß gegen Fremdschlüsseleinschränkungen

#### <a name="what-happens-when-changes-fail-to-propagate"></a>Was geschieht, wenn Änderungen nicht verteilt werden können?

-   Für die Synchronisierungsgruppe wird angezeigt, dass sie sich im Status **Warnung** befindet.
-   Details können Sie der Protokollanzeige der Portalbenutzeroberfläche entnehmen.
-   Falls das Problem nicht innerhalb von 45 Tagen behoben wird, gilt die Datenbank als veraltet.

> [!NOTE]
> Die entsprechenden Änderungen werden niemals verteilt. Die einzige Möglichkeit einer Wiederherstellung in diesem Szenario ist die erneute Erstellung der Synchronisierungsgruppe.

#### <a name="recommendation"></a>Empfehlung

Prüfen Sie über das Portal und die Protokollschnittstelle regelmäßig die Integrität der Synchronisierungsgruppe und der Datenbank.


## <a name="maintenance"></a>Wartung

### <a name="avoid-out-of-date-databases-and-sync-groups"></a><a name="avoid-out-of-date-databases-and-sync-groups"></a> Vermeiden veralteter Datenbanken und Synchronisierungsgruppen

Es kann vorkommen, dass eine Synchronisierungsgruppe oder eine Datenbank in einer Synchronisierungsgruppe nicht mehr aktuell ist. Synchronisierungsgruppen mit dem Status **Veraltet** funktionieren nicht mehr. Bei einer Datenbank mit dem Status **Veraltet** besteht die Gefahr von Datenverlusten. Es wird empfohlen, dieses Szenario zu vermeiden, anstatt die Wiederherstellung durchführen zu müssen.

#### <a name="avoid-out-of-date-databases"></a>Vermeiden veralteter Datenbanken

Eine Datenbank erhält den Status **Veraltet** , wenn sie mindestens 45 Tage lang offline war. Stellen Sie sicher, dass keine Datenbank 45 Tage oder länger offline ist, um den Datenbankstatus **Veraltet** zu vermeiden.

#### <a name="avoid-out-of-date-sync-groups"></a>Vermeiden veralteter Synchronisierungsgruppen

Eine Synchronisierungsgruppe erhält den Status **Veraltet** , wenn eine Änderung in der Synchronisierungsgruppe nicht innerhalb von 45 Tagen an die restlichen Datenbanken der Gruppe verteilt werden konnte. Prüfen Sie regelmäßig das Verlaufsprotokoll der Synchronisierungsgruppe, um den Status **Veraltet** zu vermeiden. Vergewissern Sie sich, dass alle Konflikte gelöst und Änderungen erfolgreich an die Datenbanken der Synchronisierungsgruppe verteilt wurden.

Es kann passieren, dass eine Synchronisierungsgruppe eine Änderung aus einem der folgenden Gründe nicht anwenden kann:

-   Inkompatible Schemas zwischen Tabellen
-   Inkompatible Daten zwischen Tabellen
-   Einfügung einer Zeile mit einem NULL-Wert in eine Spalte, in der keine NULL-Werte zulässig sind
-   Aktualisierung einer Zeile mit einem Wert, der gegen eine Fremdschlüsseleinschränkung verstößt

Gehen Sie wie folgt vor, um veraltete Synchronisierungsgruppen zu vermeiden:

-   Aktualisieren Sie das Schema, um die Werte aus den nicht erfolgreichen Zeilen zuzulassen.
-   Aktualisieren Sie die Fremdschlüsselwerte, um die Werte aus den nicht erfolgreichen Zeilen einzuschließen.
-   Aktualisieren Sie die Datenwerte in der nicht erfolgreichen Zeile, sodass sie mit dem Schema oder mit den Fremdschlüsseln in der Zieldatenbank kompatibel sind.

### <a name="avoid-deprovisioning-issues"></a><a name="avoid-deprovisioning-issues"></a> Vermeiden von Problemen durch die Bereitstellungsaufhebung

In einigen Fällen kann das Aufheben der Registrierung bei einer Datenbank mit einem Client-Agent dazu führen, dass die Synchronisierung nicht erfolgreich ist.

#### <a name="scenario"></a>Szenario

1. Synchronisierungsgruppe A wurde mit einer SQL-Datenbankinstanz und einer SQL Server-Datenbank erstellt, die dem lokalen Agent 1 zugeordnet ist.
2. Die gleiche lokale Datenbank ist beim lokalen Agent 2 registriert. (Dieser Agent ist keiner anderen Synchronisierungsgruppe zugeordnet.)
3. Wenn Sie die Registrierung der lokalen Datenbank beim lokalen Agent 2 aufheben, werden die Nachverfolgungs- und Metadatentabellen für Synchronisierungsgruppe A für die lokale Datenbank entfernt.
4. Bei Vorgängen der Synchronisierungsgruppe A tritt dieser Fehler auf: „The current operation could not be completed because the database is not provisioned for sync or you do not have permissions to the sync configuration tables.“ (Der aktuelle Vorgang konnte nicht abgeschlossen werden, da die Datenbank nicht für die Synchronisierung bereitgestellt wurde oder Sie nicht über Berechtigungen für die Synchronisierungskonfigurationstabellen verfügen.)

#### <a name="solution"></a>Lösung

Um dieses Szenario zu vermeiden, sollten Sie eine Datenbank nicht bei mehr als einem Agent registrieren.

Gehen Sie wie folgt vor, wenn dieser Fall bereits eingetreten ist:

1. Entfernen Sie die Datenbank aus allen Synchronisierungsgruppen, denen sie angehört.  
2. Fügen Sie die Datenbank wieder den Synchronisierungsgruppen hinzu, aus denen Sie sie entfernt haben.  
3. Stellen Sie alle betroffenen Synchronisierungsgruppen bereit. (Mit dieser Aktion wird die Datenbank bereitgestellt.)  

### <a name="modifying-a-sync-group"></a><a name="modifying-your-sync-group"></a> Ändern einer Synchronisierungsgruppe

Versuchen Sie nicht, eine Datenbank aus einer Synchronisierungsgruppe zu entfernen und dann die Synchronisierungsgruppe zu bearbeiten, ohne zunächst eine der Änderungen bereitzustellen.

Entfernen Sie stattdessen zuerst eine Datenbank aus einer Synchronisierungsgruppe. Stellen Sie dann die Änderung bereit, und warten Sie, bis die Aufhebung der Bereitstellung abgeschlossen ist. Nachdem die Aufhebung der Bereitstellung abgeschlossen ist, können Sie die Synchronisierungsgruppe bearbeiten und die Änderungen bereitstellen.

Wenn Sie versuchen, eine Datenbank zu entfernen und dann eine Synchronisierungsgruppe zu bearbeiten, ohne zuerst eine der Änderungen bereitzustellen, schlägt einer der Vorgänge fehl. Die Portaloberfläche wird unter Umständen inkonsistent. Aktualisieren Sie in diesem Fall die Seite, um den korrekten Zustand wiederherzustellen.

### <a name="avoid-schema-refresh-timeout"></a>Vermeiden eines Timeouts bei der Schemaaktualisierung

Bei der Synchronisierung eines komplexen Schemas kann es während der Schemaaktualisierung zu einem Vorgangstimeout kommen, wenn die Datenbank für Synchronisierungsmetadaten eine niedrigere SKU (beispielsweise Basic) aufweist. 

#### <a name="solution"></a>Lösung

Skalieren Sie Ihre Datenbank für Synchronisierungsmetadaten auf eine höhere SKU (beispielsweise S3) hoch. 

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur SQL-Datensynchronisierung finden Sie unter:

-   Übersicht: [Synchronisieren von Daten über mehrere Cloud- und lokale Datenbanken mit SQL-Datensynchronisierung](sql-data-sync-data-sql-server-sql-database.md)
-   Einrichten der SQL-Datensynchronisierung
    - Im Portal – [Tutorial: Einrichten der SQL-Datensynchronisierung zum Synchronisieren von Daten zwischen Azure SQL-Datenbank und SQL Server](sql-data-sync-sql-server-configure.md)
    - Mit PowerShell
        -  [Verwenden von PowerShell zum Synchronisieren zwischen mehreren Datenbanken in Azure SQL-Datenbank](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [Verwenden von PowerShell zum Synchronisieren zwischen einer Datenbank in SQL-Datenbank und einer Datenbank in einer SQL Server-Instanz](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Datensynchronisierungs-Agent: [Datensynchronisierungs-Agent für die Azure SQL-Datensynchronisierung](sql-data-sync-agent-overview.md)
-   Überwachung: [Überwachen der SQL-Datensynchronisierung mit Azure Monitor-Protokollen](./monitor-tune-overview.md)
-   Problembehandlung: [Behandeln von Problemen mit der Azure SQL-Datensynchronisierung](sql-data-sync-troubleshoot.md)
-   Aktualisieren des Synchronisierungsschemas
    -   Mit Transact-SQL: [Automatisieren der Replikation von Schemaänderungen in der Azure SQL-Datensynchronisierung](sql-data-sync-update-sync-schema.md)
    -   Mit PowerShell: [Verwenden von PowerShell zum Aktualisieren des Synchronisierungsschemas in einer bestehenden Synchronisierungsgruppe](scripts/update-sync-schema-in-sync-group.md)

Weitere Informationen zu SQL-Datenbank finden Sie hier:

-   [Übersicht über die SQL-Datenbank](sql-database-paas-overview.md)
-   [Datenbank-Lebenszyklusverwaltung](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))