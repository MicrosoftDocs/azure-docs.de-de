---
title: Was ist die SQL-Datensynchronisierung für Azure?
description: Diese Übersicht stellt die SQL-Datensynchronisierung für Azure vor, mit der Sie Daten über mehrere Cloud- und lokale Datenbanken hinweg synchronisieren können.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync, sqldbrb=1, fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/20/2019
ms.openlocfilehash: b23b5a81fdff8a05742092f517128e08723103fc
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531138"
---
# <a name="what-is-sql-data-sync-for-azure"></a>Was ist die SQL-Datensynchronisierung für Azure?

SQL-Datensynchronisierung ist ein Dienst, der auf Azure SQL-Datenbank basiert und mit dem Sie die ausgewählten Daten bidirektional über mehrere Datenbanken hinweg synchronisieren können, sowohl lokal als auch in der Cloud. 

> [!IMPORTANT]
> Die Azure SQL-Datensynchronisierung unterstützt derzeit nicht Azure SQL Managed Instance.


## <a name="overview"></a>Übersicht 

Grundlage der Datensynchronisierung ist eine Synchronisierungsgruppe. Eine Synchronisierungsgruppe ist eine Gruppe von Datenbanken, die Sie synchronisieren möchten.

Für die Datensynchronisierung wird eine Topologie der Art „Nabe und Speiche“ genutzt, um Daten zu synchronisieren. Sie definieren eine der Datenbanken in der Synchronisierungsgruppe als Hub-Datenbank. Die übrigen Datenbanken sind Mitgliedsdatenbanken. Die Synchronisierung erfolgt nur zwischen dem Hub und den einzelnen Mitgliedern.

- Die **Hub-Datenbank** muss eine Azure SQL-Datenbank sein.
- Die **Mitgliedsdatenbanken** können entweder Datenbanken in Azure SQL-Datenbank oder in Instanzen von SQL Server sein.
- Die **Synchronisierungs-Metadatendatenbank** enthält die Metadaten und das Protokoll für die Datensynchronisierung. Bei der Synchronisierungs-Metadatendatenbank muss es sich um eine Azure SQL-Datenbank handeln, die sich in derselben Region wie die Hub-Datenbank befindet. Die Synchronisierungs-Metadatendatenbank wird vom Kunden erstellt und befindet sich in seinem Besitz. Sie können nur eine Synchronisierungs-Metadatendatenbank pro Region und Abonnement verwenden. Die Synchronisierungs-Metadatendatenbank kann nicht gelöscht oder umbenannt werden, wenn Synchronisierungsgruppen oder Synchronisierungs-Agents vorhanden sind. Microsoft empfiehlt, eine neue, leere Datenbank als Synchronisierungs-Metadatendatenbank zu erstellen. Durch die Datensynchronisierung werden Tabellen in Datenbanken erstellt und eine häufige Workload ausgeführt.

> [!NOTE]
> Wenn Sie eine lokale Datenbank als Mitgliedsdatenbank verwenden, ist es erforderlich, einen [lokalen Synchronisierungs-Agent zu installieren und zu konfigurieren](sql-data-sync-sql-server-configure.md#add-on-prem).

![Synchronisieren von Daten zwischen Datenbanken](./media/sql-data-sync-data-sql-server-sql-database/sync-data-overview.png)

Eine Synchronisierungsgruppe hat die folgenden Eigenschaften:

- Im **Synchronisierungsschema** wird beschrieben, welche Daten synchronisiert werden.
- Die **Synchronisierungsrichtung** kann bidirektional oder unidirektional sein. Für die Synchronisierungsrichtung kann also *Vom Hub zum Mitglied*, *Vom Mitglied zum Hub* oder beides gelten.
- Mit dem **Synchronisierungsintervall** wird die Häufigkeit der Synchronisierung angegeben.
- Die **Richtlinie zur Konfliktlösung** ist eine Richtlinie auf Gruppenebene, die den Typ *Hub gewinnt* oder *Mitglied gewinnt* haben kann.

## <a name="when-to-use"></a>Verwendung

Die Datensynchronisierung ist nützlich, wenn Daten über mehrere Datenbanken in Azure SQL-Datenbank oder SQL Server hinweg auf dem neuesten Stand gehalten werden müssen. Hier sind die wichtigsten Anwendungsfälle für die Datensynchronisierung aufgeführt:

- **Synchronisierung von Hybriddaten:** Mit der Datensynchronisierung können Sie Daten zwischen Ihren Datenbanken in SQL Server und Azure SQL-Datenbank synchron halten, um Hybridanwendungen zu ermöglichen. Diese Funktion ist unter Umständen gut für Kunden geeignet, die eine Umstellung auf die Cloud erwägen und einen Teil ihrer Anwendung in Azure anordnen möchten.
- **Verteilte Anwendungen:** In vielen Fällen ist es vorteilhaft, unterschiedliche Workloads auf verschiedene Datenbanken aufzuteilen. Wenn Sie beispielsweise über eine große Produktionsdatenbank verfügen, aber gleichzeitig die Berichterstellung oder Analyse für diese Daten durchführen müssen, ist für diese zusätzliche Workload die Verwendung einer zweiten Datenbank hilfreich. Bei diesem Ansatz werden die Auswirkungen auf die Leistung Ihrer Produktionsworkload reduziert. Sie können die Datensynchronisierung nutzen, um diese beiden Datenbanken synchron zu halten.
- **Global verteilte Anwendungen:** Viele Unternehmen sind in mehreren Regionen und sogar mehreren Ländern/Regionen ansässig. Es ist ratsam, die Daten jeweils in einer Region in der Nähe vorzuhalten, um die Netzwerklatenz zu verringern. Mit der Datensynchronisierung können Sie Datenbanken in den Regionen weltweit synchron halten.

Die Datensynchronisierung ist für folgende Szenarios nicht die beste Lösung:

| Szenario | Einige empfohlene Lösungen |
|----------|----------------------------|
| Notfallwiederherstellung | [Georedundante Sicherungen in Azure](automated-backups-overview.md) |
| Leseskalierung | [Verwenden von schreibgeschützten Replikaten für den Lastenausgleich schreibgeschützter Abfrageworkloads (Vorschau)](read-scale-out.md) |
| ETL (OLTP zu OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) oder [SQL Server Integration Services](/sql/integration-services/sql-server-integration-services) |
| Migration von SQL Server zu Azure SQL-Datenbank. Mit der SQL-Datensynchronisierung kann jedoch nach Abschluss der Migration sichergestellt werden, dass Quelle und Ziel synchron bleiben.  | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="how-it-works"></a>Funktionsweise

- **Nachverfolgen von Datenänderungen:** Bei der Datensynchronisierung werden Änderungen mithilfe von Auslösern für Einfügen, Aktualisieren und Löschen nachverfolgt. Die Änderungen werden in der Benutzerdatenbank in einer Nebentabelle aufgezeichnet. Beachten Sie, dass BULK INSERT standardmäßig keine Trigger auslöst. Wenn FIRE_TRIGGERS nicht angegeben ist, werden keine Einfügungstrigger ausgeführt. Fügen Sie die Option FIRE_TRIGGERS hinzu, damit die Datensynchronisierung diese Einfügungen verfolgen kann. 
- **Synchronisieren von Daten:** Für die Datensynchronisierung wird ein Hub-and-Spoke-Modell genutzt. Der Hub wird einzeln mit jedem Mitglied synchronisiert. Änderungen auf dem Hub werden für das Mitglied heruntergeladen, und anschließend werden Änderungen vom Mitglied auf den Hub hochgeladen.
- **Beheben von Konflikten:** Die Datensynchronisierung bietet zwei Optionen für die Lösung von Konflikten, und zwar *Hub gewinnt* und *Mitglied gewinnt*.
  - Wenn Sie *Hub gewinnt* wählen, werden die Änderungen auf dem Mitglied immer durch die Änderungen des Hub überschrieben.
  - Bei Auswahl von *Mitglied gewinnt* werden die Änderungen auf dem Hub durch die Änderungen auf dem Mitglied überschrieben. Falls mehr als ein Mitglied vorhanden ist, hängt der endgültige Wert davon ab, welches Mitglied zuerst synchronisiert wird.

## <a name="compare-with-transactional-replication"></a>Vergleichen mit der Transaktionsreplikation

| | Datensynchronisierung | Transaktionsreplikation |
|---|---|---|
| **Vorteile** | – Aktiv/Aktiv-Unterstützung<br/>– Bidirektional zwischen lokaler und Azure SQL-Datenbank | – Niedrigere Latenzzeiten<br/>– Transaktionskonsistenz<br/>– Wiederverwendung vorhandener Topologie nach der Migration <br/>– Unterstützung von Azure SQL Managed Instance |
| **Nachteile** | – Keine Transaktionskonsistenz<br/>– Größere Auswirkung auf die Leistung | – Keine Veröffentlichung aus Azure SQL-Datenbank <br/>– Hohe Wartungskosten |

## <a name="get-started"></a>Erste Schritte 

### <a name="set-up-data-sync-in-the-azure-portal"></a>Einrichten der Datensynchronisierung im Azure-Portal

- [Einrichten von Azure SQL-Datensynchronisierung](sql-data-sync-sql-server-configure.md)
- Datensynchronisierungs-Agent: [Datensynchronisierungs-Agent für die Azure SQL-Datensynchronisierung](sql-data-sync-agent-overview.md)

### <a name="set-up-data-sync-with-powershell"></a>Einrichten der Datensynchronisierung mit PowerShell

- [Verwenden von PowerShell zum Synchronisieren zwischen mehreren Datenbanken in Azure SQL-Datenbank](scripts/sql-data-sync-sync-data-between-sql-databases.md)
- [Verwenden von PowerShell zum Synchronisieren zwischen einer Datenbank in Azure SQL-Datenbank und einer Datenbank in einer SQL Server-Instanz](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Überprüfen der bewährten Methoden für die Datensynchronisierung

- [Best practices for Azure SQL Data Sync (Preview)](sql-data-sync-best-practices.md) (Bewährte Methoden für die Azure SQL-Datensynchronisierung-Vorschauversion)

### <a name="did-something-go-wrong"></a>Ist etwas schiefgegangen?

- [Troubleshoot issues with SQL Data Sync (Preview)](./sql-data-sync-troubleshoot.md) (Behandeln von Problemen mit der Azure SQL-Datensynchronisierung-Vorschauversion)

## <a name="consistency-and-performance"></a>Konsistenz und Leistung

### <a name="eventual-consistency"></a>Letztliche Konsistenz

Die Transaktionskonsistenz ist nicht garantiert, da die Datensynchronisierung auf Auslösern basiert. Microsoft gewährleistet, dass alle Änderungen letztlich vorgenommen werden und dass es bei der Datensynchronisierung nicht zu Datenverlusten kommt.

### <a name="performance-impact"></a>Auswirkungen auf die Leistung

Für die Datensynchronisierung werden Auslöser für Einfügen, Aktualisieren und Löschen verwendet, um Änderungen nachzuverfolgen. In der Benutzerdatenbank werden Nebentabellen für die Änderungsnachverfolgung erstellt. Diese Aktivitäten zur Änderungsnachverfolgung haben Auswirkungen auf Ihre Datenbankworkload. Bewerten Sie Ihre Dienstebene, und aktualisieren Sie sie bei Bedarf.

Das Bereitstellen und Aufheben der Bereitstellung während der Erstellung, Aktualisierung oder Löschung von Synchronisierungsgruppen kann sich ebenfalls nachteilig auf die Datenbankleistung auswirken.

## <a name="requirements-and-limitations"></a><a name="sync-req-lim"></a> Anforderungen und Einschränkungen

### <a name="general-requirements"></a>Allgemeine Anforderungen

- Jede Tabelle muss über einen Primärschlüssel verfügen. Ändern Sie nicht den Wert des Primärschlüssels in einer Zeile. Wenn Sie einen Primärschlüsselwert ändern müssen, können Sie die Zeile löschen und mit dem neuen Wert des Primärschlüssels neu erstellen.

> [!IMPORTANT]
> Wenn Sie den Wert eines vorhandenen Primärschlüssels ändern, führt dies zu folgendem fehlerhaften Verhalten:
> - Daten zwischen Hub und Mitglied können verloren gehen, auch wenn bei der Synchronisierung kein Problem gemeldet wird.
> - Bei der Synchronisierung kann ein Fehler auftreten, weil die Nachverfolgungstabelle aufgrund der Primärschlüsseländerung über eine nicht vorhandene Zeile aus der Quelle verfügt.

- Momentaufnahmeisolation muss sowohl für die Synchronisierungsmitglieder als auch den Hub aktiviert sein. Weitere Informationen finden Sie unter [Momentaufnahmeisolation in SQL Server](/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Allgemeine Einschränkungen

- Eine Tabelle kann keine Identitätsspalte enthalten, die kein Primärschlüssel ist.
- Eine Tabelle muss einen gruppierten Index enthalten, der bei der Datensynchronisierung verwendet werden soll.
- Ein Primärschlüssel kann nicht über die folgenden Datentypen verfügen: sql_variant, binary, varbinary, image, xml.
- Gehen Sie mit Bedacht vor, wenn Sie die folgenden Datentypen als Primärschlüssel verwenden, da nur die Genauigkeit bis auf die Sekunde unterstützt wird: time, datetime, datetime2, datetimeoffset.
- Die Namen von Objekten (Datenbanken, Tabellen und Spalten) dürfen nicht die druckbaren Zeichen Punkt (.), linke eckige Klammer ([) oder rechte eckige Klammer (]) enthalten.
- Ein Tabellenname darf keine druckbaren Zeichen enthalten: ! " # $ % ' ( ) * + - Leerzeichen
- Die Azure Active Directory-Authentifizierung wird nicht unterstützt.
- Gibt es Tabellen, die denselben Namen, aber unterschiedlichen Schemas haben (z. B. dbo.customers und sales.customers), kann nur eine der Tabellen in der Synchronisierung hinzugefügt werden.
- Spalten mit benutzerdefinierten Datentypen werden nicht unterstützt.
- Das Verschieben von Servern zwischen verschiedenen Abonnements wird nicht unterstützt. 

#### <a name="unsupported-data-types"></a>Nicht unterstützte Datentypen

- FileStream
- SQL/CLR UDT
- XMLSchemaCollection (XML unterstützt)
- Cursor, RowVersion, Timestamp, Hierarchyid

#### <a name="unsupported-column-types"></a>Nicht unterstützte Spaltentypen

Mit der Datensynchronisierung können keine schreibgeschützten oder vom System generierten Spalten synchronisiert werden. Beispiel:

- Berechnete Spalten.
- Vom System generierte Spalten für temporale Tabellen

#### <a name="limitations-on-service-and-database-dimensions"></a>Einschränkungen von Dienst- und Datenbankdimensionen

| **Dimensionen**                                                  | **Begrenzung**              | **Problemumgehung**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Maximale Anzahl von Synchronisierungsgruppen, denen eine Datenbank angehören kann       | 5                      |                             |
| Maximale Anzahl von Endpunkten einer einzelnen Synchronisierungsgruppe              | 30                     |                             |
| Maximale Anzahl von lokalen Endpunkten in einer einzelnen Synchronisierungsgruppe | 5                      | Erstellen mehrerer Synchronisierungsgruppen |
| Namen von Datenbanken, Tabellen, Schemas und Spalten                       | 50 Zeichen pro Name |                             |
| Tabellen in einer Synchronisierungsgruppe                                          | 500                    | Erstellen mehrerer Synchronisierungsgruppen |
| Spalten in einer Tabelle einer Synchronisierungsgruppe                              | 1000                   |                             |
| Größe von Datenzeilen in einer Tabelle                                        | 24 Mb                  |                             |

> [!NOTE]
> Es können bis zu 30 Endpunkte in einer einzelnen Synchronisierungsgruppe vorhanden sein, wenn es nur eine Synchronisierungsgruppe gibt. Wenn mehr als eine Synchronisierungsgruppe vorhanden ist, darf die Gesamtanzahl der Endpunkte in allen Synchronisierungsgruppen 30 nicht überschreiten. Wenn eine Datenbank mehreren Synchronisierungsgruppen angehört, wird sie als mehrere Endpunkte und nicht als einer gezählt.

### <a name="network-requirements"></a>Netzwerkanforderungen

Wenn die Synchronisierungsgruppe eingerichtet ist, muss der Datensynchronisierungsdienst eine Verbindung mit der Hub-Datenbank herstellen. Zu dem Zeitpunkt, an dem Sie die Synchronisierungsgruppe einrichten, muss der Azure SQL-Server in seinen Einstellungen für `Firewalls and virtual networks` die folgende Konfiguration aufweisen:

 * *Zugriff auf öffentliches Netzwerk verweigern* muss auf *Aus* festgelegt werden.
 * *Anderen Azure-Diensten und -Ressourcen den Zugriff auf diesen Server gestatten* muss auf *Ja* festgelegt werden, oder Sie müssen IP-Regeln für die vom [Datensynchronisationsdienst verwendeten IP-Adressen erstellen](network-access-controls-overview.md#data-sync).

Nachdem die Synchronisierungsgruppe erstellt und bereitgestellt wurde, können Sie diese Einstellungen deaktivieren. Der Synchronisierungs-Agent stellt eine direkte Verbindung mit der Hub-Datenbank her, und Sie können die [Firewall-IP-Regeln](firewall-configure.md) oder [privaten Endpunkte](private-endpoint-overview.md) des Servers verwenden, um dem Agent den Zugriff auf den Hubserver zu gestatten.

> [!NOTE]
> Wenn Sie die Schemaeinstellungen der Synchronisierungsgruppe ändern, müssen Sie dem Datensynchronisierungsdienst den Zugriff auf den Server erneut gestatten, damit die Hub-Datenbank erneut bereitgestellt werden kann.

## <a name="faq-about-sql-data-sync"></a>Häufig gestellte Fragen zur SQL-Datensynchronisierung

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Kosten für den SQL-Datensynchronisierungsdienst

Für den SQL-Datensynchronisierungsdienst selbst fallen keine Kosten an. Es fallen aber weiterhin Datenübertragungsgebühren für die ein- und ausgehende Datenverschiebung für Ihre SQL-Datenbankinstanz an. Weitere Informationen finden Sie unter [SQL-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Für die Datensynchronisierung unterstützte Regionen

Die SQL-Datensynchronisierung ist in allen Regionen verfügbar.

### <a name="is-a-sql-database-account-required"></a>Ist ein SQL-Datenbank-Konto erforderlich?

Ja. Sie müssen über ein SQL-Datenbank-Konto zum Hosten der Hub-Datenbank verfügen.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-databases-only"></a>Kann ich die Datensynchronisierung verwenden, um Daten ausschließlich für SQL Server-Datenbanken synchronisieren zu lassen?

Nicht direkt. Sie können Daten zwischen SQL Server-Datenbanken jedoch indirekt synchronisieren, indem Sie in Azure eine Hub-Datenbank erstellen und anschließend die lokalen Datenbanken der Synchronisierungsgruppe hinzufügen.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-subscriptions"></a>Kann ich die Datensynchronisierung verwenden, um Datenbanken in SQL-Datenbank zu synchronisieren, die zu unterschiedlichen Abonnements gehören?

Ja. Sie können Daten zwischen Datenbanken aus Ressourcengruppen synchronisieren, die zu unterschiedlichen Abonnements gehören.

- Wenn die Abonnements zum gleichen Mandanten gehören und Sie über Berechtigungen für alle Abonnements verfügen, können Sie die Synchronisierungsgruppe im Azure-Portal konfigurieren.
- Andernfalls müssen die Synchronisierungsmitglieder unterschiedlicher Abonnements mithilfe von PowerShell hinzugefügt werden.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Kann ich die Datensynchronisierung zum Synchronisieren zwischen Datenbanken in SQL-Datenbank verwenden, die zu verschiedenen Clouds gehören (etwa zur öffentlichen Azure-Cloud und Azure China 21Vianet)?

Ja. Sie können zwischen Datenbanken synchronisieren, die zu verschiedenen Clouds gehören. Sie müssen PowerShell verwenden, um die Synchronisierungsmitglieder hinzuzufügen, die zu den verschiedenen Abonnements gehören.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Kann ich mithilfe der Datensynchronisierung ein Seeding für Daten aus meiner Produktionsdatenbank in eine leere Datenbank ausführen und die Daten dann synchronisieren?

Ja. Erstellen Sie das Schema in der neuen Datenbank mithilfe eines Skripts manuell, das sich am Original orientiert. Nachdem Sie das Schema erstellt haben, fügen Sie Tabellen einer Synchronisierungsgruppe hinzu, um die Daten zu kopieren und synchron zu halten.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Sollte ich die SQL-Datensynchronisierung nutzen, um meine Datenbanken zu sichern und wiederherzustellen?

Es wird nicht empfohlen, die SQL-Datensynchronisierung zum Erstellen einer Sicherung Ihrer Daten zu verwenden. Sie können keine Sicherung und Wiederherstellung für einen bestimmten Zeitpunkt durchführen, da Synchronisierungen mit der SQL-Datensynchronisierung keine Versionsangaben aufweisen. Zudem werden mit der SQL-Datensynchronisierung keine anderen SQL-Objekte gesichert, z. B. gespeicherte Prozeduren, und es kann kein schneller Wiederherstellungsvorgang durchgeführt werden.

Informationen zu einem empfohlenen Sicherungsverfahren finden Sie unter [Kopieren einer Datenbank in Azure SQL-Datenbank](database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Kann die Datensynchronisierung verschlüsselte Tabellen und Spalten synchronisieren?

- Wenn eine Datenbank Always Encrypted verwendet, können nur die *nicht* verschlüsselten Tabellen und Spalten synchronisiert werden. Die verschlüsselten Spalten können nicht synchronisiert werden, da die Datensynchronisierung die Daten nicht entschlüsseln kann.
- Wenn eine Spalte die Verschlüsselung auf Spaltenebene (Column-Level Encryption, CLE) verwendet, können Sie die Spalte synchronisieren, sofern die Zeilengröße die maximal zulässige Größe von 24 MB nicht übersteigt. Die Datensynchronisierung behandelt die durch einen Schlüssel verschlüsselte Spalte (CLE) als gewöhnliche Binärdaten. Um die Daten auf anderen Synchronisierungsmitgliedern entschlüsseln zu können, benötigen Sie das gleiche Zertifikat.

### <a name="is-collation-supported-in-sql-data-sync"></a>Wird die Sortierung für die SQL-Datensynchronisierung unterstützt?

Ja. Für die SQL-Datensynchronisierung wird die Sortierung in den folgenden Szenarien unterstützt:

- Falls sich die gewählten Schematabellen für die Synchronisierung nicht bereits in Ihren Hub- oder Mitgliedsdatenbanken befinden, erstellt der Dienst während der Bereitstellung der Synchronisierungsgruppe durch Sie automatisch die entsprechenden Tabellen und Spalten, und die Sortierungseinstellungen sind in den leeren Zieldatenbanken ausgewählt.
- Wenn die zu synchronisierenden Tabellen bereits sowohl in Ihren Hub-Datenbanken als auch in Ihren Mitgliedsdatenbanken vorhanden sind, müssen für die SQL-Datensynchronisierung die Primärschlüsselspalten die gleiche Sortierung in der Hub- und Mitgliedsdatenbank aufweisen, damit die Synchronisierungsgruppe erfolgreich bereitgestellt werden kann. Für andere Spalten als die Primärschlüsselspalten gelten keine Sortierungseinschränkungen.

### <a name="is-federation-supported-in-sql-data-sync"></a>Wird der Verbund für die SQL-Datensynchronisierung unterstützt?

Eine Datenbank für den Verbundstamm kann im SQL-Datensynchronisierungsdienst ohne Einschränkungen verwendet werden. Es ist nicht möglich, den Endpunkt der Verbunddatenbank zur aktuellen Version der SQL-Datensynchronisierung hinzuzufügen.

### <a name="can-i-use-data-sync-to-sync-data-exported-from-dynamics-365-using-bring-your-own-database-byod-feature"></a>Kann ich die Datensynchronisierung verwenden, um Daten zu synchronisieren, die aus Dynamics 365 unter Verwendung der BYOD-Funktion (Bring Your Own Database) exportiert wurden?

Mit dem BYOD-Feature von Dynamics 365 können Administratoren Datenentitäten aus der Anwendung in ihre eigene Microsoft Azure SQL-Datenbank exportieren. Die Datensynchronisierung kann verwendet werden, um diese Daten mit anderen Datenbanken zu synchronisieren, wenn die Daten mit einem **inkrementellen Pushvorgang** exportiert werden (vollständige Pushvorgänge werden nicht unterstützt) und **Trigger in Zieldatenbank aktivieren** auf **Ja** festgelegt ist.

## <a name="next-steps"></a>Nächste Schritte

### <a name="update-the-schema-of-a-synced-database"></a>Aktualisieren des Schemas einer synchronisierten Datenbank

Müssen Sie das Schema einer Datenbank in einer Synchronisierungsgruppe aktualisieren? Schemaänderungen werden nicht automatisch repliziert. Einige Lösungen finden Sie in den folgenden Artikeln:

- [Automatisieren der Replikation von Schemaänderungen mit der SQL-Datensynchronisierung in Azure](./sql-data-sync-update-sync-schema.md)
- [Verwenden von PowerShell zum Aktualisieren des Synchronisierungsschemas in einer bestehenden Synchronisierungsgruppe](scripts/update-sync-schema-in-sync-group.md)

### <a name="monitor-and-troubleshoot"></a>Überwachen und Behandeln von Problemen

Wird die SQL-Datensynchronisierung wie erwartet ausgeführt? Informationen zum Überwachen der Aktivität und Behandeln von Problemen finden Sie in den folgenden Artikeln:

- [Überwachen der SQL-Datensynchronisierung mit Azure Monitor-Protokollen](./monitor-tune-overview.md)
- [Troubleshoot issues with SQL Data Sync (Preview)](./sql-data-sync-troubleshoot.md) (Behandeln von Problemen mit der Azure SQL-Datensynchronisierung-Vorschauversion)

### <a name="learn-more-about-azure-sql-database"></a>Weitere Informationen zu Azure SQL-Datenbank

Weitere Informationen zu Azure SQL-Datenbank finden Sie in den folgenden Artikeln:

- [Übersicht über die SQL-Datenbank](sql-database-paas-overview.md)
- [Datenbank-Lebenszyklusverwaltung](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
