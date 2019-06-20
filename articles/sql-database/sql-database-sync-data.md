---
title: Azure SQL-Datensynchronisierung | Microsoft-Dokumentation
description: Diese Übersicht bietet eine Einführung in die Azure SQL-Datensynchronisierung.
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
ms.date: 01/25/2019
ms.openlocfilehash: cfa94fc1c75bcd1eaa9a076cfe63369f60ce5f1c
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693084"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Synchronisieren von Daten über mehrere Cloud- und lokale Datenbanken mit SQL-Datensynchronisierung

SQL-Datensynchronisierung ist ein Dienst, der auf Azure SQL-Datenbank basiert und mit dem Sie die ausgewählten Daten bidirektional über mehrere SQL-Datenbanken und SQL Server-Instanzen hinweg synchronisieren können.

> [!IMPORTANT]
> Die Azure SQL-Datensynchronisierung unterstützt derzeit **keine** verwalteten Azure SQL-Datenbank-Instanzen.

## <a name="when-to-use-data-sync"></a>Verwenden der Datensynchronisierung

Die Datensynchronisierung ist nützlich, wenn Daten über mehrere Azure SQL-Datenbanken oder SQL Server-Datenbanken hinweg auf dem neuesten Stand gehalten werden müssen. Hier sind die wichtigsten Anwendungsfälle für die Datensynchronisierung aufgeführt:

- **Hybriddatensynchronisierung:** Mit der Datensynchronisierung können Sie Daten zwischen Ihren lokalen Datenbanken und Azure SQL-Datenbanken synchron halten, um Hybridanwendungen zu ermöglichen. Diese Funktion ist unter Umständen gut für Kunden geeignet, die eine Umstellung auf die Cloud erwägen und einen Teil ihrer Anwendung in Azure anordnen möchten.
- **Verteilte Anwendungen:** In vielen Fällen ist es vorteilhaft, unterschiedliche Workloads auf verschiedene Datenbanken aufzuteilen. Wenn Sie beispielsweise über eine große Produktionsdatenbank verfügen, aber gleichzeitig die Berichterstellung oder Analyse für diese Daten durchführen müssen, ist für diese zusätzliche Workload die Verwendung einer zweiten Datenbank hilfreich. Bei diesem Ansatz werden die Auswirkungen auf die Leistung Ihrer Produktionsworkload reduziert. Sie können die Datensynchronisierung nutzen, um diese beiden Datenbanken synchron zu halten.
- **Global verteilte Anwendungen:** Viele Unternehmen sind in mehreren Regionen und sogar mehreren Ländern/Regionen ansässig. Es ist ratsam, die Daten jeweils in einer Region in der Nähe vorzuhalten, um die Netzwerklatenz zu verringern. Mit der Datensynchronisierung können Sie Datenbanken in den Regionen weltweit synchron halten.

Die Datensynchronisierung ist für folgende Szenarien nicht die beste Lösung:

| Szenario | Einige empfohlene Lösungen |
|----------|----------------------------|
| Notfallwiederherstellung | [Georedundante Sicherungen in Azure](sql-database-automated-backups.md) |
| Leseskalierung | [Verwenden von schreibgeschützten Replikaten für den Lastenausgleich schreibgeschützter Abfrageworkloads (Vorschau)](sql-database-read-scale-out.md) |
| ETL (OLTP zu OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) oder [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| Migration von einer lokalen SQL Server-Instanz zu Azure SQL-Datenbank | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="overview-of-sql-data-sync"></a>Übersicht über die SQL-Datensynchronisierung

Grundlage der Datensynchronisierung ist eine Synchronisierungsgruppe. Eine Synchronisierungsgruppe ist eine Gruppe von Datenbanken, die Sie synchronisieren möchten.

Für die Datensynchronisierung wird eine Topologie der Art „Nabe und Speiche“ genutzt, um Daten zu synchronisieren. Sie definieren eine der Datenbanken in der Synchronisierungsgruppe als Hub-Datenbank. Die übrigen Datenbanken sind Mitgliedsdatenbanken. Die Synchronisierung erfolgt nur zwischen dem Hub und den einzelnen Mitgliedern.

- Die **Hub-Datenbank** muss eine Azure SQL-Datenbank sein.
- Die **Mitgliedsdatenbanken** können entweder SQL-Datenbanken, lokale SQL Server-Datenbanken oder SQL Server-Instanzen auf virtuellen Azure-Computern sein.
- Die **Synchronisierungsdatenbank** enthält die Metadaten und das Protokoll für die Datensynchronisierung. Bei der Synchronisierungsdatenbank muss es sich um eine Azure SQL-Datenbank handeln, die in derselben Region wie die Hub-Datenbank angeordnet ist. Die Synchronisierungsdatenbank wird vom Kunden erstellt und befindet sich in seinem Besitz.

> [!NOTE]
> Wenn Sie eine lokale Datenbank als Mitgliedsdatenbank verwenden, ist es erforderlich, einen [lokalen Synchronisierungs-Agent zu installieren und zu konfigurieren](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Synchronisieren von Daten zwischen Datenbanken](media/sql-database-sync-data/sync-data-overview.png)

Eine Synchronisierungsgruppe hat die folgenden Eigenschaften:

- Im **Synchronisierungsschema** wird beschrieben, welche Daten synchronisiert werden.
- Die **Synchronisierungsrichtung** kann bidirektional oder unidirektional sein. Für die Synchronisierungsrichtung kann also *Vom Hub zum Mitglied*, *Vom Mitglied zum Hub* oder beides gelten.
- Mit dem **Synchronisierungsintervall** wird die Häufigkeit der Synchronisierung angegeben.
- Die **Richtlinie zur Konfliktlösung** ist eine Richtlinie auf Gruppenebene, die den Typ *Hub gewinnt* oder *Mitglied gewinnt* haben kann.

## <a name="how-does-data-sync-work"></a>Funktionsweise der Datensynchronisierung

- **Nachverfolgen von Datenänderungen:** Bei der Datensynchronisierung werden Änderungen mithilfe von Auslösern für Einfügen, Aktualisieren und Löschen nachverfolgt. Die Änderungen werden in der Benutzerdatenbank in einer Nebentabelle aufgezeichnet. Beachten Sie, dass BULK INSERT standardmäßig keine Trigger auslöst. Wenn FIRE_TRIGGERS nicht angegeben ist, werden keine Einfügungstrigger ausgeführt. Fügen Sie die Option FIRE_TRIGGERS hinzu, damit die Datensynchronisierung diese Einfügungen verfolgen kann. 
- **Synchronisieren von Daten:** Für die Datensynchronisierung wird ein Hub-Spoke-Modell („Nabe und Speiche“) genutzt. Der Hub (Nabe) wird einzeln mit jedem Mitglied synchronisiert. Änderungen auf dem Hub werden für das Mitglied heruntergeladen, und anschließend werden Änderungen vom Mitglied auf den Hub hochgeladen.
- **Beheben von Konflikten:** Die Datensynchronisierung bietet zwei Optionen für die Lösung von Konflikten, und zwar *Hub gewinnt* und *Mitglied gewinnt*.
  - Wenn Sie *Hub gewinnt* wählen, werden die Änderungen auf dem Mitglied immer durch die Änderungen des Hub überschrieben.
  - Bei Auswahl von *Mitglied gewinnt* werden die Änderungen auf dem Hub durch die Änderungen auf dem Mitglied überschrieben. Falls mehr als ein Mitglied vorhanden ist, hängt der endgültige Wert davon ab, welches Mitglied zuerst synchronisiert wird.

## <a name="compare-data-sync-with-transactional-replication"></a>Vergleichen von Datensynchronisierung und Transaktionsreplikation

| | Datensynchronisierung | Transaktionsreplikation |
|---|---|---|
| Vorteile | – Aktiv/Aktiv-Unterstützung<br/>– Bidirektional zwischen lokaler und Azure SQL-Datenbank | – Niedrigere Latenzzeiten<br/>– Transaktionskonsistenz<br/>– Wiederverwendung vorhandener Topologie nach der Migration |
| Nachteile | – Latenzzeiten von 5 Minuten und mehr<br/>– Keine Transaktionskonsistenz<br/>– Größere Auswirkung auf die Leistung | – Keine Veröffentlichung über eine Einzel- oder Pooldatenbank in Azure SQL-Datenbank<br/>– Hohe Wartungskosten |
| | | |

## <a name="get-started-with-sql-data-sync"></a>Erste Schritte mit der SQL-Datensynchronisierung

### <a name="set-up-data-sync-in-the-azure-portal"></a>Einrichten der Datensynchronisierung im Azure-Portal

- [Einrichten von Azure SQL-Datensynchronisierung](sql-database-get-started-sql-data-sync.md)
- Datensynchronisierungs-Agent: [Datensynchronisierungs-Agent für die Azure SQL-Datensynchronisierung](sql-database-data-sync-agent.md)

### <a name="set-up-data-sync-with-powershell"></a>Einrichten der Datensynchronisierung mit PowerShell

- [Verwenden von PowerShell zum Synchronisieren zwischen mehreren Azure SQL-Datenbanken](scripts/sql-database-sync-data-between-sql-databases.md)
- [Verwenden von PowerShell zum Synchronisieren zwischen einer Azure SQL-Datenbank und einer lokalen SQL Server-Datenbank](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Überprüfen der bewährten Methoden für die Datensynchronisierung

- [Best practices for Azure SQL Data Sync (Preview)](sql-database-best-practices-data-sync.md) (Bewährte Methoden für die Azure SQL-Datensynchronisierung-Vorschauversion)

### <a name="did-something-go-wrong"></a>Ist etwas schiefgegangen?

- [Troubleshoot issues with SQL Data Sync (Preview)](sql-database-troubleshoot-data-sync.md) (Behandeln von Problemen mit der Azure SQL-Datensynchronisierung-Vorschauversion)

## <a name="consistency-and-performance"></a>Konsistenz und Leistung

#### <a name="eventual-consistency"></a>Letztliche Konsistenz

Die Transaktionskonsistenz ist nicht garantiert, da die Datensynchronisierung auf Auslösern basiert. Microsoft gewährleistet, dass alle Änderungen letztlich vorgenommen werden und dass es bei der Datensynchronisierung nicht zu Datenverlusten kommt.

#### <a name="performance-impact"></a>Auswirkungen auf die Leistung

Für die Datensynchronisierung werden Auslöser für Einfügen, Aktualisieren und Löschen verwendet, um Änderungen nachzuverfolgen. In der Benutzerdatenbank werden Nebentabellen für die Änderungsnachverfolgung erstellt. Diese Aktivitäten zur Änderungsnachverfolgung haben Auswirkungen auf Ihre Datenbankworkload. Bewerten Sie Ihre Dienstebene, und aktualisieren Sie sie bei Bedarf.

Das Bereitstellen und Aufheben der Bereitstellung während der Erstellung, Aktualisierung oder Löschung von Synchronisierungsgruppen kann sich ebenfalls nachteilig auf die Datenbankleistung auswirken. 

## <a name="sync-req-lim"></a> Anforderungen und Einschränkungen

### <a name="general-requirements"></a>Allgemeine Anforderungen

- Jede Tabelle muss über einen Primärschlüssel verfügen. Ändern Sie nicht den Wert des Primärschlüssels in einer Zeile. Wenn Sie einen Primärschlüsselwert ändern müssen, können Sie die Zeile löschen und mit dem neuen Wert des Primärschlüssels neu erstellen. 
- Die Momentaufnahmeisolation muss aktiviert sein. Weitere Informationen finden Sie unter [Momentaufnahmeisolation in SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Allgemeine Einschränkungen

- Eine Tabelle kann keine Identitätsspalte enthalten, die kein Primärschlüssel ist.
- Ein Primärschlüssel kann nicht über die folgenden Datentypen verfügen: sql_variant, binary, varbinary, image, xml. 
- Gehen Sie mit Bedacht vor, wenn Sie die folgenden Datentypen als Primärschlüssel verwenden, da nur die Genauigkeit bis auf die Sekunde unterstützt wird: time, datetime, datetime2, datetimeoffset.
- Die Namen von Objekten (Datenbanken, Tabellen und Spalten) dürfen nicht die druckbaren Zeichen Punkt (.), linke eckige Klammer ([) oder rechte eckige Klammer (]) enthalten.
- Die Azure Active Directory-Authentifizierung wird nicht unterstützt.
- Tabellen mit demselben Namen, aber unterschiedlichem Schema (z.B. „dbo.customers“ und „sales.customers“) werden nicht unterstützt.
- Spalten mit benutzerdefinierten Datentypen werden nicht unterstützt.

#### <a name="unsupported-data-types"></a>Nicht unterstützte Datentypen

- FileStream
- SQL/CLR UDT
- XMLSchemaCollection (XML unterstützt)
- Cursor, RowVersion, Timestamp, Hierarchyid

#### <a name="unsupported-column-types"></a>Nicht unterstützte Spaltentypen

Mit der Datensynchronisierung können keine schreibgeschützten oder vom System generierten Spalten synchronisiert werden. Beispiel:

- Berechnete Spalten
- Vom System generierte Spalten für temporale Tabellen

#### <a name="limitations-on-service-and-database-dimensions"></a>Einschränkungen von Dienst- und Datenbankdimensionen

| **Dimensionen**                                                      | **Begrenzung**              | **Problemumgehung**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Maximale Anzahl von Synchronisierungsgruppen, denen eine Datenbank angehören kann       | 5                      |                             |
| Maximale Anzahl von Endpunkten einer einzelnen Synchronisierungsgruppe              | 30                     |                             |
| Maximale Anzahl von lokalen Endpunkten in einer einzelnen Synchronisierungsgruppe | 5                      | Erstellen mehrerer Synchronisierungsgruppen |
| Namen von Datenbanken, Tabellen, Schemas und Spalten                       | 50 Zeichen pro Name |                             |
| Tabellen in einer Synchronisierungsgruppe                                          | 500                    | Erstellen mehrerer Synchronisierungsgruppen |
| Spalten in einer Tabelle einer Synchronisierungsgruppe                              | 1000                   |                             |
| Größe von Datenzeilen in einer Tabelle                                        | 24 Mb                  |                             |
| Minimales Synchronisierungsintervall                                           | 5 Minuten              |                             |
|||
> [!NOTE]
> Es können bis zu 30 Endpunkte in einer einzelnen Synchronisierungsgruppe vorhanden sein, wenn es nur eine Synchronisierungsgruppe gibt. Wenn mehr als eine Synchronisierungsgruppe vorhanden ist, darf die Gesamtanzahl der Endpunkte in allen Synchronisierungsgruppen 30 nicht überschreiten. Wenn eine Datenbank mehreren Synchronisierungsgruppen angehört, wird sie als mehrere Endpunkte und nicht als einer gezählt.

## <a name="faq-about-sql-data-sync"></a>Häufig gestellte Fragen zur SQL-Datensynchronisierung

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Kosten für den SQL-Datensynchronisierungsdienst

Für den SQL-Datensynchronisierungsdienst selbst fallen keine Kosten an.  Es fallen aber weiterhin Datenübertragungsgebühren für die ein- und ausgehende Datenverschiebung für Ihre SQL-Datenbankinstanz an. Weitere Informationen finden Sie unter [SQL-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Für die Datensynchronisierung unterstützte Regionen

Die SQL-Datensynchronisierung ist in allen Regionen verfügbar.

### <a name="is-a-sql-database-account-required"></a>Ist ein SQL-Datenbank-Konto erforderlich?

Ja. Sie müssen über ein SQL-Datenbankkonto zum Hosten der Hub-Datenbank verfügen.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Kann ich die Datensynchronisierung verwenden, um Daten ausschließlich für lokale SQL Server-Datenbanken synchronisieren zu lassen?

Nicht direkt. Sie können Daten zwischen lokalen SQL Server-Datenbanken jedoch indirekt synchronisieren, indem Sie in Azure eine Hub-Datenbank erstellen und anschließend die lokalen Datenbanken der Synchronisierungsgruppe hinzufügen.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Kann ich die Datensynchronisierung zwischen SQL-Datenbanken verschiedener Abonnements verwenden?

Ja. Sie können Daten zwischen SQL-Datenbanken aus Ressourcengruppen synchronisieren, die zu unterschiedlichen Abonnements gehören.

- Wenn die Abonnements zum gleichen Mandanten gehören und Sie über Berechtigungen für alle Abonnements verfügen, können Sie die Synchronisierungsgruppe im Azure-Portal konfigurieren.
- Andernfalls müssen die Synchronisierungsmitglieder unterschiedlicher Abonnements mithilfe von PowerShell hinzugefügt werden.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china"></a>Kann ich die Datensynchronisierung zwischen SQL-Datenbanken verschiedener Clouds (etwa öffentliche Azure-Cloud und Azure China) verwenden?

Ja. Die Synchronisierung zwischen SQL-Datenbanken, die zu verschiedenen Clouds gehören, ist möglich. Sie müssen PowerShell verwenden, um die Synchronisierungsmitglieder der verschiedenen Abonnements hinzuzufügen.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Kann ich mithilfe der Datensynchronisierung ein Seeding für Daten aus meiner Produktionsdatenbank in eine leere Datenbank ausführen und die Daten dann synchronisieren?

Ja. Erstellen Sie das Schema in der neuen Datenbank mithilfe eines Skripts manuell, das sich am Original orientiert. Nachdem Sie das Schema erstellt haben, fügen Sie Tabellen einer Synchronisierungsgruppe hinzu, um die Daten zu kopieren und synchron zu halten.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Sollte ich die SQL-Datensynchronisierung nutzen, um meine Datenbanken zu sichern und wiederherzustellen?

Es wird nicht empfohlen, die SQL-Datensynchronisierung zum Erstellen einer Sicherung Ihrer Daten zu verwenden. Sie können keine Sicherung und Wiederherstellung auf einen bestimmten Zeitpunkt durchführen, da Synchronisierungen mit der SQL-Datensynchronisierung keine Versionsangaben aufweisen. Zudem werden mit der SQL-Datensynchronisierung keine anderen SQL-Objekte gesichert, z.B. gespeicherte Prozeduren, und es kann kein schneller Wiederherstellungsvorgang durchgeführt werden.

Informationen zu einem empfohlenen Sicherungsverfahren finden Sie unter [Kopieren einer Azure SQL-Datenbank](sql-database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Kann die Datensynchronisierung verschlüsselte Tabellen und Spalten synchronisieren?

- Wenn eine Datenbank Always Encrypted verwendet, können nur die *nicht* verschlüsselten Tabellen und Spalten synchronisiert werden. Die verschlüsselten Spalten können nicht synchronisiert werden, da die Datensynchronisierung die Daten nicht entschlüsseln kann.
- Wenn eine Spalte die Verschlüsselung auf Spaltenebene (Column-Level Encryption, CLE) verwendet, können Sie die Spalte synchronisieren, sofern die Zeilengröße die maximal zulässige Größe von 24 MB nicht übersteigt. Die Datensynchronisierung behandelt die durch einen Schlüssel verschlüsselte Spalte (CLE) als gewöhnliche Binärdaten. Um die Daten auf anderen Synchronisierungsmitgliedern entschlüsseln zu können, benötigen Sie das gleiche Zertifikat.

### <a name="is-collation-supported-in-sql-data-sync"></a>Wird die Sortierung für die SQL-Datensynchronisierung unterstützt?

Ja. Für die SQL-Datensynchronisierung wird die Sortierung in den folgenden Szenarien unterstützt:

- Falls sich die gewählten Schematabellen für die Synchronisierung nicht bereits in Ihren Hub- oder Mitgliedsdatenbanken befinden, erstellt der Dienst während der Bereitstellung der Synchronisierungsgruppe durch Sie automatisch die entsprechenden Tabellen und Spalten, und die Sortierungseinstellungen sind in den leeren Zieldatenbanken ausgewählt.
- Wenn die zu synchronisierenden Tabellen bereits sowohl in Ihren Hub-Datenbanken als auch in Ihren Mitgliedsdatenbanken vorhanden sind, müssen für die SQL-Datensynchronisierung die Primärschlüsselspalten die gleiche Sortierung in der Hub- und Mitgliedsdatenbank aufweisen, damit die Synchronisierungsgruppe erfolgreich bereitgestellt werden kann. Für andere Spalten als die Primärschlüsselspalten gelten keine Sortierungseinschränkungen.

### <a name="is-federation-supported-in-sql-data-sync"></a>Wird der Verbund für die SQL-Datensynchronisierung unterstützt?

Eine Datenbank für den Verbundstamm kann im SQL-Datensynchronisierungsdienst ohne Einschränkungen verwendet werden. Es ist nicht möglich, den Endpunkt der Verbunddatenbank zur aktuellen Version der SQL-Datensynchronisierung hinzuzufügen.

## <a name="next-steps"></a>Nächste Schritte

### <a name="update-the-schema-of-a-synced-database"></a>Aktualisieren des Schemas einer synchronisierten Datenbank

Müssen Sie das Schema einer Datenbank in einer Synchronisierungsgruppe aktualisieren? Schemaänderungen werden nicht automatisch repliziert. Einige Lösungen finden Sie in den folgenden Artikeln:

- [Automatisieren der Replikation von Schemaänderungen in der Azure SQL-Datensynchronisierung](sql-database-update-sync-schema.md)
- [Verwenden von PowerShell zum Aktualisieren des Synchronisierungsschemas in einer bestehenden Synchronisierungsgruppe](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>Überwachen und Behandeln von Problemen

Wird die SQL-Datensynchronisierung wie erwartet ausgeführt? Informationen zum Überwachen der Aktivität und Behandeln von Problemen finden Sie in den folgenden Artikeln:

- [Überwachen der Azure SQL-Datensynchronisierung mit Azure Monitor-Protokollen](sql-database-sync-monitor-oms.md)
- [Troubleshoot issues with SQL Data Sync (Preview)](sql-database-troubleshoot-data-sync.md) (Behandeln von Problemen mit der Azure SQL-Datensynchronisierung-Vorschauversion)

### <a name="learn-more-about-azure-sql-database"></a>Weitere Informationen zu Azure SQL-Datenbank

Weitere Informationen zu SQL-Datenbank finden Sie in den folgenden Artikeln:

- [Übersicht über die SQL-Datenbank](sql-database-technical-overview.md)
- [Datenbank-Lebenszyklusverwaltung](https://msdn.microsoft.com/library/jj907294.aspx)
