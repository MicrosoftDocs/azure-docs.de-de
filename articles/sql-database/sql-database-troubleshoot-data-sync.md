---
title: Behandeln von Problemen mit der Azure SQL-Datensynchronisierung | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie häufige Probleme mit der Azure SQL-Datensynchronisierung behandeln.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
manager: craigg
ms.date: 12/20/2018
ms.openlocfilehash: 4e2808378834a0270586ce674e1043ca443320c5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60331195"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>Behandeln von Problemen mit der SQL-Datensynchronisierung

Dieser Artikel beschreibt, wie Sie bekannte Probleme mit der Azure SQL-Datensynchronisierung behandeln. Sofern eine Problemlösung verfügbar ist, ist sie hier angegeben.

Eine Übersicht über die SQL-Datensynchronisierung finden Sie unter [Synchronisieren von Daten über mehrere Cloud- und lokale Datenbanken mit SQL-Datensynchronisierung](sql-database-sync-data.md).

> [!IMPORTANT]
> Die Azure SQL-Datensynchronisierung unterstützt zurzeit **keine** verwalteten Azure SQL-Datenbank-Instanzen.

## <a name="sync-issues"></a>Probleme bei der Synchronisierung

- [Die Synchronisierung lokaler Datenbanken, die dem Client-Agent zugeordnet sind, ist über die Portalbenutzeroberfläche nicht erfolgreich.](#sync-fails)

- [Meine Synchronisierungsgruppe ist im Verarbeitungszustand hängengeblieben.](#sync-stuck)

- [Meine Tabellen enthalten fehlerhafte Daten.](#sync-baddata)

- [Nach einer erfolgreichen Synchronisierung sind inkonsistente Primärschlüsseldaten vorhanden.](#sync-pkdata)

- [Die Leistung hat sich erheblich verschlechtert.](#sync-perf)

- [Die folgende Meldung wird angezeigt: „Der Wert NULL kann nicht in die Spalte \< einfügt werden>. da die Spalte keine NULL-Werte zulässt. Was bedeutet dies, und wie kann ich das Problem beheben?](#sync-nulls)

- [Wie geht die Datensynchronisierung mit Zirkelbezügen um? Diese liegen vor, wenn dieselben Daten in mehreren Synchronisierungsgruppen synchronisiert werden und sich daher ständig ändern.](#sync-circ)

### <a name="sync-fails"></a>Die Synchronisierung lokaler Datenbanken, die dem Client-Agent zugeordnet sind, ist über die Portalbenutzeroberfläche nicht erfolgreich.

Die Synchronisierung lokaler Datenbanken, die dem Client-Agent zugeordnet sind, ist über die Benutzeroberfläche des SQL-Datensynchronisierungsportals nicht erfolgreich. Auf dem lokalen Computer, auf dem der Agent ausgeführt wird, enthält das Ereignisprotokoll Fehler vom Typ „System.IO.IOException“. Diese Fehler weisen darauf hin, dass auf dem Datenträger nicht genügend Speicherplatz zur Verfügung steht.

- **Ursache**. Auf dem Laufwerk ist nicht genügend Speicherplatz verfügbar.

- **Lösung**. Schaffen Sie mehr Platz auf dem Laufwerk, auf dem sich das Verzeichnis „%TEMP%“ befindet.

### <a name="sync-stuck"></a>Meine Synchronisierungsgruppe ist im Verarbeitungszustand hängengeblieben.

Eine Synchronisierungsgruppe in der SQL-Datensynchronisierung bleibt lange Zeit im Verarbeitungszustand. Sie reagiert nicht auf den **stop**-Befehl, und die Protokolle zeigen keine neuen Einträge.

Folgende Bedingungen können dazu führen, dass eine Synchronisierungsgruppe im Verarbeitungszustand hängenbleibt:

- **Ursache**. Der Client-Agent ist offline.

- **Lösung**. Vergewissern Sie sich, dass der Client-Agent online ist, und versuchen Sie es dann noch mal.

- **Ursache**. Der Client-Agent wurde deinstalliert oder ist aus einem anderen Grund nicht vorhanden.

- **Lösung**. Falls der Client-Agent deinstalliert wurde oder nicht vorhanden ist, gehen Sie wie folgt vor:

    1. Entfernen Sie die Agent-XML-Datei aus dem Installationsordner der SQL-Datensynchronisierung (sofern die Datei vorhanden ist).
    1. Installieren Sie den Agent auf einem lokalen Computer (hierbei kann es sich um denselben oder einen anderen Computer handeln). Übermitteln Sie dann den Agent-Schlüssel, der im Portal für den als offline angezeigten Agent generiert wurde.

- **Ursache**. Der SQL-Datensynchronisierungsdienst wurde beendet.

- **Lösung**. Starten Sie den SQL-Datensynchronisierungsdienst neu.

    1. Suchen Sie im Menü **Start** nach **Dienste**.
    1. Klicken Sie in den Suchergebnissen auf **Dienste**.
    1. Suchen Sie den **SQL-Datensynchronisierungsdienst**.
    1. Falls der Status **Beendet** lautet, klicken Sie mit der rechten Maustaste auf den Dienstnamen, und wählen Sie dann **Starten** aus.

> [!NOTE]
> Wenn die obigen Informationen nicht ausreichen, um Ihre Synchronisierungsgruppe aus dem Verarbeitungszustand zu lösen, kann der Microsoft-Support den Status der Synchronisierungsgruppe zurücksetzen. Um den Status der Synchronisierungsgruppe zurücksetzen zu lassen, erstellen Sie einen Beitrag im [Forum zu Azure SQL-Datenbank](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted). Teilen Sie in diesem Beitrag Ihre Abonnement-ID und die ID der Synchronisierungsgruppe mit, die zurückgesetzt werden soll. Ein Microsoft-Supportmitarbeiter antwortet auf Ihren Beitrag und informiert Sie, wenn der Status zurückgesetzt wurde.

### <a name="sync-baddata"></a> Meine Tabellen enthalten fehlerhafte Daten.

Wenn Tabellen, die den gleichen Namen aufweisen, aber aus verschiedenen Datenbankschemas stammen, in eine Synchronisierung eingeschlossen werden, werden nach der Synchronisierung falsche Daten in den Tabellen angezeigt.

- **Ursache**. Der Bereitstellungsprozess der SQL-Datensynchronisierung verwendet die gleichen Nachverfolgungstabellen für Tabellen mit gleichem Namen, aber unterschiedlichen Schemas. Aus diesem Grund spiegeln sich Änderungen in beiden Tabellen in der gleichen Nachverfolgungstabelle wider. Dies führt zu falschen Daten während der Synchronisierung.

- **Lösung**. Achten Sie darauf, dass sich die Namen der zu synchronisierenden Tabellen unterscheiden, auch wenn sie zu unterschiedlichen Schemas in der Datenbank gehören.

### <a name="sync-pkdata"></a> Nach einer erfolgreichen Synchronisierung sind inkonsistente Primärschlüsseldaten vorhanden.

Eine Synchronisierung wird als erfolgreich gemeldet, und das Protokoll enthält keine fehlerhaften oder übersprungenen Zeilen enthält, aber Sie stellen fest, dass Primärschlüsseldaten in den Datenbanken in der Synchronisierungsgruppe nicht konsistent sind.

- **Ursache**. Dieses Ergebnis ist programmbedingt. Änderungen in einer Primärschlüsselspalte führen zu inkonsistenten Daten in den Zeilen, in denen der Primärschlüssel geändert wurde.

- **Lösung**. Achten Sie zur Vermeidung dieses Problems darauf, dass keine Daten in einer Primärschlüsselspalte geändert werden. Um das Problem zu beheben, löschen Sie die Zeile mit den inkonsistenten Daten von allen Endpunkten in der Synchronisierungsgruppe. Fügen Sie die Zeile dann erneut ein.

### <a name="sync-perf"></a> Die Leistung hat sich erheblich verschlechtert.

Die Leistung verschlechtert sich deutlich – unter Umständen so weit, dass Sie nicht einmal mehr die Benutzeroberfläche der Datensynchronisierung starten können.

- **Ursache**. Dies ist höchstwahrscheinlich auf eine Synchronisierungsschleife zurückzuführen. Eine Synchronisierungsschleife tritt auf, wenn eine Synchronisierung von Synchronisierungsgruppe A eine Synchronisierung von Synchronisierungsgruppe B auslöst, die dann eine Synchronisierung von Synchronisierungsgruppe A auslöst. Die tatsächliche Situation kann komplexer sein und mehr als zwei Synchronisierungsgruppen in der Schleife umfassen. Das Problem ist hier ein zirkuläres Auslösen der Synchronisierung, das dadurch verursacht wird, dass Synchronisierungsgruppen sich überlappen.

- **Lösung**. Die beste Lösung ist Prävention. Vergewissern Sie sich, dass Ihre Synchronisierungsgruppen keine Zirkelbezüge enthalten. Eine Zeile, die von einer Synchronisierungsgruppe synchronisiert wird, darf nicht von einer anderen Synchronisierungsgruppe synchronisiert werden.

### <a name="sync-nulls"></a>Die folgende Meldung wird angezeigt: „Der Wert NULL kann nicht in die Spalte \< einfügt werden>. da die Spalte keine NULL-Werte zulässt. Was bedeutet dies, und wie kann ich das Problem beheben? 
Diese Fehlermeldung besagt, dass eines der beiden folgenden Probleme aufgetreten ist:
-  Eine Tabelle weist keinen Primärschlüssel auf. Um dieses Problem zu beheben, fügen Sie allen Tabellen, die Sie synchronisieren möchten, einen Primärschlüssel hinzu.
-  Es gibt eine WHERE-Klausel in Ihrer CREATE INDEX-Anweisung. Die Datensynchronisierung verarbeitet diese Bedingung nicht. Um dieses Problem zu beheben, entfernen Sie die WHERE-Klausel, oder nehmen Sie die Änderungen an allen Datenbanken manuell vor. 
 
### <a name="sync-circ"></a> Wie geht die Datensynchronisierung mit Zirkelbezügen um? Diese liegen vor, wenn dieselben Daten in mehreren Synchronisierungsgruppen synchronisiert werden und sich daher ständig ändern.
Die Datensynchronisierung beseitigt Zirkelbezüge nicht. Vermeiden Sie sie deshalb unbedingt. 

## <a name="client-agent-issues"></a>Probleme mit dem Client-Agent

Informationen zur Problembehandlung beim Client-Agent finden Sie unter [Beheben von Problemen mit dem Datensynchronisierungs-Agent](sql-database-data-sync-agent.md#agent-tshoot).

## <a name="setup-and-maintenance-issues"></a>Probleme bei Setup und Wartung

- [Ich erhalte eine Meldung mit dem Hinweis, dass auf dem Datenträger kein Speicherplatz mehr zur Verfügung steht.](#setup-space)

- [Ich kann meine Synchronisierungsgruppe nicht löschen.](#setup-delete)

- [Ich kann die Registrierung einer lokalen SQL Server-Datenbank nicht aufheben.](#setup-unreg)

- [Ich bin nicht zum Starten von Systemdiensten berechtigt.](#setup-perms)

- [Eine Datenbank hat den Status „Veraltet“.](#setup-date)

- [Eine Synchronisierungsgruppe hat den Status „Veraltet“.](#setup-date2)

- [Eine Synchronisierungsgruppe kann innerhalb von drei Minuten nach dem Deinstallieren oder Beenden des Agents nicht gelöscht werden.](#setup-delete2)

- [Was geschieht beim Wiederherstellen einer verloren gegangenen oder beschädigten Datenbank?](#setup-restore)

### <a name="setup-space"></a> Ich erhalte eine Meldung mit dem Hinweis, dass auf dem Datenträger kein Speicherplatz mehr zur Verfügung steht.

- **Ursache**. Die Meldung, dass auf dem Datenträger kein Speicherplatz mehr zur Verfügung steht, kann angezeigt werden, wenn übrig gebliebene Dateien gelöscht werden müssen. Dies kann durch eine Virenschutzsoftware verursacht werden oder daran liegen, dass während Löschversuchen Dateien geöffnet waren.

- **Lösung**. Löschen Sie die Synchronisierungsdateien, die sich im Ordner „%temp%“ befinden, manuell (`del \*sync\* /s`). Löschen Sie dann die Unterverzeichnisse im Ordner „%temp%“.

> [!IMPORTANT]
> Löschen Sie keine Dateien, während eine Synchronisierung ausgeführt wird.

### <a name="setup-delete"></a> Ich kann meine Synchronisierungsgruppe nicht löschen.

Der Versuch, eine Synchronisierungsgruppe zu löschen, ist nicht erfolgreich. Jedes der folgenden Szenarien kann dazu führen, dass eine Synchronisierungsgruppe sich nicht löschen lässt:

- **Ursache**. Der Client-Agent ist offline.

- **Lösung**. Vergewissern Sie sich, dass der Client-Agent online ist, und versuchen Sie es dann noch mal.

- **Ursache**. Der Client-Agent wurde deinstalliert oder ist aus einem anderen Grund nicht vorhanden.

- **Lösung**. Falls der Client-Agent deinstalliert wurde oder nicht vorhanden ist, gehen Sie wie folgt vor:  
    a. Entfernen Sie die Agent-XML-Datei aus dem Installationsordner der SQL-Datensynchronisierung (sofern die Datei vorhanden ist).  
    b. Installieren Sie den Agent auf einem lokalen Computer (hierbei kann es sich um denselben oder einen anderen Computer handeln). Übermitteln Sie dann den Agent-Schlüssel, der im Portal für den als offline angezeigten Agent generiert wurde.

- **Ursache**. Eine Datenbank ist offline.

- **Lösung**. Vergewissern Sie sich, dass alle SQL-Datenbanken und SQL Server-Datenbanken online sind.

- **Ursache**. Die Synchronisierungsgruppe führt einen Bereitstellungs- oder Synchronisierungsvorgang aus.

- **Lösung**. Warten Sie, bis der Bereitstellungs- oder Synchronisierungsvorgang beendet ist, und versuchen Sie dann erneut, die Synchronisierungsgruppe zu löschen.

### <a name="setup-unreg"></a> Ich kann die Registrierung einer lokalen SQL Server-Datenbank nicht aufheben.

- **Ursache**. Wahrscheinlich versuchen Sie, die Registrierung einer bereits gelöschten Datenbank aufzuheben.

- **Lösung**. Wenn Sie die Registrierung einer lokalen SQL Server-Datenbank aufheben möchten, wählen Sie die Datenbank aus, und klicken Sie anschließend auf **Löschen erzwingen**.

  Sollte die Datenbank dadurch nicht aus der Synchronisierungsgruppe entfernt werden, gehen Sie folgendermaßen vor:

  1. Beenden Sie den Client-Agent-Hostdienst, und starten Sie ihn neu:  
    a. Klicken Sie auf das Menü **Start**.  
    b. Geben Sie **services.msc** in das Suchfeld ein.  
    c. Doppelklicken Sie im Ergebnisbereich im Abschnitt **Programme** auf **Dienste**.  
    d. Klicken Sie mit der rechten Maustaste auf den **SQL-Datensynchronisierungsdienst**.  
    e. Falls der Dienst ausgeführt wird, beenden Sie ihn.  
    f. Klicken Sie mit der rechten Maustaste auf den Dienst, und wählen Sie **Starten** aus.  
    g. Überprüfen Sie, ob die Datenbank immer noch registriert ist. Falls sie nicht mehr registriert ist, sind keine weiteren Schritte erforderlich. Fahren Sie andernfalls mit dem nächsten Schritt fort.
  1. Öffnen Sie die Client-Agent-App (SqlAzureDataSyncAgent).
  1. Wählen Sie **Anmeldeinformationen bearbeiten** aus, und geben Sie die Anmeldeinformationen für die Datenbank ein.
  1. Fahren Sie mit der Aufhebung der Registrierung fort.

### <a name="setup-perms"></a> Ich bin nicht zum Starten von Systemdiensten berechtigt.

- **Ursache**. Dieser Fehler tritt in zwei Situationen auf:
  -   Der Benutzername/das Kennwort ist falsch.
  -   Das angegebene Benutzerkonto verfügt nicht über ausreichende Berechtigungen für die Anmeldung als Dienst.

- **Lösung**. Erteilen Sie dem Benutzerkonto die Berechtigung für die Anmeldung als Dienst.

  1. Wechseln Sie zu **Start** > **Systemsteuerung** > **Verwaltung** > **Lokale Sicherheitsrichtlinie** > **Lokale Richtlinien** > **Zuweisen von Benutzerrechten**.
  1. Wählen Sie **Anmelden als Dienst** aus.
  1. Klicken Sie im Dialogfeld **Eigenschaften** auf das Benutzerkonto.
  1. Klicken Sie auf **Apply** (Anwenden) und dann auf **OK**.
  1. Schließen Sie alle Fenster.

### <a name="setup-date"></a> Eine Datenbank hat den Status „Veraltet“.

- **Ursache**. Datenbanken, die mindestens 45 Tage vom Dienst getrennt waren (gemessen ab dem Zeitpunkt, zu dem die Datenbank offline geschaltet wurde), werden von der SQL-Datensynchronisierung entfernt. Eine Datenbank, die online geschaltet wird, nachdem sie mindestens 45 Tage offline war, weist den Status **Veraltet** auf.

- **Lösung**. Sie können den Status **Veraltet** vermeiden, indem Sie sicherstellen, dass keine Ihrer Datenbanken 45 Tage oder länger offline ist.

  Wenn eine Datenbank den Status **Veraltet** aufweist, gehen Sie folgendermaßen vor:

  1. Entfernen Sie die Datenbank mit dem Status **Veraltet** aus der Synchronisierungsgruppe.
  1. Fügen Sie die Datenbank wieder der Synchronisierungsgruppe hinzu.

  > [!WARNING]
  > Änderungen, die an der Datenbank vorgenommen wurden, während sie offline war, gehen verloren.

### <a name="setup-date2"></a> Eine Synchronisierungsgruppe hat den Status „Veraltet“.

- **Ursache**. Falls eine Änderung innerhalb der gesamten Aufbewahrungszeit von 45 Tagen nicht angewendet werden kann, kann eine Synchronisierungsgruppe den Status „Veraltet“ erhalten.

- **Lösung**. Um den Status **Veraltet** zu vermeiden, überprüfen Sie regelmäßig die Ergebnisse Ihrer Synchronisierungsaufträge in der Verlaufsanzeige. Untersuchen Sie alle Änderungen, die nicht angewendet werden konnten, und behandeln Sie die entsprechenden Probleme.

  Wenn eine Synchronisierungsgruppe den Status **Veraltet** aufweist, löschen Sie die Gruppe und erstellen sie neu.

### <a name="setup-delete2"></a> Eine Synchronisierungsgruppe kann innerhalb von drei Minuten nach dem Deinstallieren oder Beenden des Agents nicht gelöscht werden.

Sie können eine Synchronisierungsgruppe nach dem Deinstallieren oder Beenden des zugeordneten Client-Agents für die SQL-Datensynchronisierung drei Minuten lang nicht löschen.

- **Lösung**.

  1. Entfernen Sie eine Synchronisierungsgruppe, während die zugeordneten Synchronisierungs-Agents online sind (empfohlen).
  1. Falls der Agent installiert, aber offline ist, schalten Sie ihn auf dem lokalen Computer online. Warten Sie, bis der Agent im Portal der SQL-Datensynchronisierung als **online** angezeigt wird. Entfernen Sie dann die Synchronisierungsgruppe.
  1. Ist der Agent offline, weil er deinstalliert wurde, gehen Sie folgendermaßen vor:  
    a.  Entfernen Sie die Agent-XML-Datei aus dem Installationsordner der SQL-Datensynchronisierung (sofern die Datei vorhanden ist).  
    b.  Installieren Sie den Agent auf einem lokalen Computer (hierbei kann es sich um denselben oder einen anderen Computer handeln). Übermitteln Sie dann den Agent-Schlüssel, der im Portal für den als offline angezeigten Agent generiert wurde.  
    c. Versuchen Sie, die Synchronisierungsgruppe zu löschen.

### <a name="setup-restore"></a> Was geschieht beim Wiederherstellen einer verloren gegangenen oder beschädigten Datenbank?

Wenn Sie eine verloren gegangene oder beschädigte Datenbank aus einer Sicherung wiederherstellen, stellt sich möglicherweise keine Konvergenz der Daten in den Synchronisierungsgruppen ein, zu denen die Datenbank gehört.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur SQL-Datensynchronisierung finden Sie unter:

-   Übersicht: [Synchronisieren von Daten über mehrere Cloud- und lokale Datenbanken mit SQL-Datensynchronisierung](sql-database-sync-data.md)
-   Einrichten der Datensynchronisierung
    - Im Portal – [Tutorial: Einrichten der SQL-Datensynchronisierung zum Synchronisieren von Daten zwischen Azure SQL-Datenbank und lokalem SQL Server](sql-database-get-started-sql-data-sync.md)
    - Mit PowerShell
        -  [Verwenden von PowerShell zum Synchronisieren zwischen mehreren Azure SQL-Datenbanken](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Verwenden von PowerShell zum Synchronisieren zwischen einer Azure SQL-Datenbank und einer lokalen SQL Server-Datenbank](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Datensynchronisierungs-Agent: [Datensynchronisierungs-Agent für die Azure SQL-Datensynchronisierung](sql-database-data-sync-agent.md)
-   Bewährte Methoden: [Bewährte Methoden für die Azure SQL-Datensynchronisierung](sql-database-best-practices-data-sync.md)
-   Überwachung: [Überwachen der SQL-Datensynchronisierung mit Azure Monitor-Protokollen](sql-database-sync-monitor-oms.md)
-   Aktualisieren des Synchronisierungsschemas
    -   Mit Transact-SQL: [Automatisieren der Replikation von Schemaänderungen in der Azure SQL-Datensynchronisierung](sql-database-update-sync-schema.md)
    -   Mit PowerShell: [Verwenden von PowerShell zum Aktualisieren des Synchronisierungsschemas in einer bestehenden Synchronisierungsgruppe](scripts/sql-database-sync-update-schema.md)

Weitere Informationen zu SQL-Datenbank finden Sie hier:

-   [Übersicht über die SQL-Datenbank](sql-database-technical-overview.md)
-   [Datenbank-Lebenszyklusverwaltung](https://msdn.microsoft.com/library/jj907294.aspx)
