---
title: Sichern und Wiederherstellen – Azure Database for PostgreSQL – Einzelserver
description: Hier finden Sie Informationen zu automatischen Sicherungen und zur Wiederherstellung Ihres Azure Database for PostgreSQL-Einzelservers.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: e74c96e0c03d75f34a16d95d0bed642c1900f558
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219722"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---single-server"></a>Sicherung und Wiederherstellung in Azure Database for PostgreSQL – Einzelserver

Für Azure Database for PostgreSQL werden Sicherungen automatisch erstellt und in einem vom Benutzer konfigurierten lokal redundanten oder georedundanten Speicher gespeichert. Sicherungen können verwendet werden, um für Ihren Server den Stand zu einem bestimmten Zeitpunkt wiederherzustellen. Sicherungen und Wiederherstellungen sind wesentliche Bestandteile jeder Strategie für Geschäftskontinuität, da Ihre Daten so vor versehentlichen Beschädigungen und Löschungen geschützt werden.

## <a name="backups"></a>Backups

Azure Database for PostgreSQL nimmt Sicherungen der Datendateien und der Transaktionsprotokolle vor. Abhängig von der unterstützten maximalen Speichergröße werden entweder vollständige oder differenzielle Sicherungen (Server mit maximal 4 TB Speicher) oder Sicherungsmomentaufnahmen (Server mit bis zu 16 TB Speicher) angelegt. Dank dieser Sicherungen können Sie für einen Server den Stand zu einem beliebigen Zeitpunkt wiederherstellen, der innerhalb Ihres konfigurierten Aufbewahrungszeitraums für Sicherungen liegt. Die Standardaufbewahrungsdauer für Sicherungen beträgt sieben Tage. Optional können Sie einen Zeitraum von bis zu 35 Tagen festlegen. Zur Verschlüsselung aller Sicherungen wird die AES-Verschlüsselung mit 256 Bit verwendet.

Diese Sicherungsdateien können nicht exportiert werden. Die Sicherungen können nur für Wiederherstellungsvorgänge in Azure Database for PostgreSQL verwendet werden. Zum Kopieren einer Datenbank können Sie [pg_dump](howto-migrate-using-dump-and-restore.md) verwenden.

### <a name="backup-frequency"></a>Sicherungshäufigkeit

#### <a name="servers-with-up-to-4-tb-storage"></a>Server mit bis zu 4 TB Speicher

Bei Servern, die bis zu 4 TB Speicher unterstützen, erfolgt jede Woche eine vollständige Sicherung. Differenzielle Sicherungen werden zweimal täglich ausgeführt. Transaktionsprotokollsicherungen finden alle fünf Minuten statt.


#### <a name="servers-with-up-to-16-tb-storage"></a>Server mit bis zu 16 TB Speicher

In einigen [Azure-Regionen](./concepts-pricing-tiers.md#storage) unterstützen alle neu bereitgestellten Server bis zu 16 TB Speicher. Die Sicherungen auf diesen großen Speicherservern basieren auf Momentaufnahmen. Die erste vollständige Momentaufnahmensicherung wird unmittelbar nach der Erstellung des Servers eingeplant. Diese erste vollständige Momentaufnahmensicherung wird als Basissicherung des Servers beibehalten. Nachfolgende Momentaufnahmensicherungen sind nur differenzielle Sicherungen. Differenzielle Momentaufnahmensicherungen erfolgen nicht nach einem festgelegten Zeitplan. An einem Tag werden drei differenzielle Momentaufnahmensicherungen durchgeführt. Transaktionsprotokollsicherungen finden alle fünf Minuten statt. 

### <a name="backup-retention"></a>Sicherungsaufbewahrung

Sicherungen werden basierend auf der Einstellung für den Aufbewahrungszeitraum der Sicherung auf dem Server beibehalten. Sie können einen Aufbewahrungszeitraum von 7 bis 35 Tagen auswählen. Der Standardaufbewahrungszeitraum beträgt sieben Tage. Sie können den Aufbewahrungszeitraum bei der Servererstellung oder später festlegen, indem Sie die Sicherungskonfiguration mithilfe des [Azure-Portals](./howto-restore-server-portal.md#set-backup-configuration) oder über die [Azure CLI](./howto-restore-server-cli.md#set-backup-configuration) aktualisieren. 

Mit „Aufbewahrungszeit für Sicherung“ wird auch gesteuert, für welchen zurückliegenden Zeitraum eine Point-in-Time-Wiederherstellung durchgeführt werden kann, da dies auf den verfügbaren Sicherungen basiert. Der Aufbewahrungszeitraum kann auch als Wiederherstellungsfenster im Hinblick auf die Wiederherstellung behandelt werden. Alle Sicherungen, die zum Durchführen einer Zeitpunktwiederherstellung innerhalb des Aufbewahrungszeitraums für die Sicherung erforderlich sind, werden im Sicherungsspeicher beibehalten. Wenn der Aufbewahrungszeitraum für Sicherungen beispielsweise auf sieben Tage festgelegt ist, entspricht das Wiederherstellungsfenster einer Dauer von sieben Tagen. In diesem Szenario bleiben alle Sicherungen erhalten, die zum Wiederherstellen des Servers in den letzten sieben Tagen erforderlich sind. Beispiel für Sicherungsaufbewahrungsfenster von sieben Tagen:
- Bei Servern mit bis zu 4 TB Speicher werden bis zu zwei vollständige Datenbanksicherungen, alle differenziellen Sicherungen sowie Transaktionsprotokollsicherungen beibehalten, die seit der frühesten Datenbanksicherung durchgeführt wurden.
-   Bei Servern mit bis zu 16 TB Speicher werden die vollständige Datenbankmomentaufnahme, alle differenziellen Momentaufnahmen und die Transaktionsprotokollsicherungen der letzten acht Tage beibehalten.

### <a name="backup-redundancy-options"></a>Optionen für Sicherungsredundanz

Bei Azure Database for PostgreSQL können Sie in den Tarifen „Allgemein“ und „Arbeitsspeicheroptimiert“ flexibel zwischen lokal redundantem und georedundantem Sicherungsspeicher wählen. Wenn die Sicherungen in einem georedundanten Sicherungsspeicher gespeichert werden, werden sie nicht nur in der Region vorgehalten, in der Ihr Server gehostet wird. Sie werden außerdem in einem [gekoppelten Datencenter](../best-practices-availability-paired-regions.md) repliziert. Dies erhöht den Schutz und ermöglicht in einem Notfall die Wiederherstellung Ihres Servers in einer anderen Region. Im Tarif „Basic“ ist nur lokal redundanter Sicherungsspeicher verfügbar.

> [!IMPORTANT]
> Das Konfigurieren von lokal redundantem oder georedundantem Speicher für die Sicherung ist nur während der Erstellung des Servers zulässig. Nachdem der Server bereitgestellt wurde, können Sie die Option für die Sicherungsspeicherredundanz nicht mehr ändern.

### <a name="backup-storage-cost"></a>Kosten für Sicherungsspeicher

Bei Azure Database for PostgreSQL werden bis zu 100% Ihres bereitgestellten Serverspeichers ohne zusätzliche Kosten als Sicherungsspeicher hinzugefügt. Wenn zusätzlicher Sicherungsspeicher verwendet wird, wird dies in GB pro Monat berechnet. Beispiel: Wenn Sie einen Server mit 250 GB bereitgestellt haben, verfügen Sie über 250 GB an zusätzlichem Speicher, der ohne zusätzliche Kosten für Serversicherungen zur Verfügung steht. Der für Sicherungen verwendete Speicher über 250 GB wird gemäß dem [Preismodell](https://azure.microsoft.com/pricing/details/postgresql/) abgerechnet.

Sie können die im Azure-Portal in Azure Monitor verfügbare Metrik für den [verwendeten Sicherungsspeicher](concepts-monitoring.md) zum Überwachen des von einem Server genutzten Sicherungsspeichers verwenden. Die Metrik für den verwendeten Sicherungsspeicher stellt den gesamten Speicherplatz dar, der von allen vollständigen Datenbanksicherungen, differenziellen Sicherungen und Protokollsicherungen beansprucht wurde, die auf der Grundlage des für den Server festgelegten Aufbewahrungszeitraums für Sicherungen aufbewahrt wurden. Die Häufigkeit der Sicherungen wird durch den Dienst verwaltet und wurde bereits erläutert. Eine hohe Transaktionsaktivität auf dem Server kann dazu führen, dass die Sicherungsspeicherauslastung unabhängig von der Gesamtgröße der Datenbank zunimmt. Bei georedundantem Speicher wird doppelt so viel Sicherungsspeicher genutzt wie bei lokal redundantem Speicher. 

Das primäre Mittel zum Steuern der Sicherungsspeicherkosten besteht darin, den geeigneten Aufbewahrungszeitraum festzulegen und die richtige Sicherungsredundanzoptionen auszuwählen, um die gewünschten Wiederherstellungsziele zu erreichen. Sie können als Aufbewahrungszeitraum einen Bereich von 7 bis 35 Tagen auswählen. Bei universellen und arbeitsspeicheroptimierten Servern können Sie georedundanten Speicher für Sicherungen auswählen.

## <a name="restore"></a>Restore

Wenn in Azure Database for PostgreSQL eine Wiederherstellung durchgeführt wird, wird aus den Sicherungen des ursprünglichen Servers ein neuer Server erstellt. 

Es gibt zwei Arten der Wiederherstellung:

- Die **Point-in-Time-Wiederherstellung** ist für beide Sicherungsredundanzoptionen verfügbar. Es wird ein neuer Server in derselben Region wie der ursprüngliche Server erstellt.
- Die **Geowiederherstellung** ist nur verfügbar, wenn Sie Ihren Server für georedundanten Speicher konfiguriert haben. Hierbei können Sie den Server in einer anderen Region wiederherstellen.

Die geschätzte Wiederherstellungszeit hängt von verschiedenen Faktoren ab, z.B. der Datenbankgröße, Transaktionsprotokollgröße und Netzwerkbandbreite sowie der Gesamtzahl von Datenbanken, die gleichzeitig in derselben Region wiederhergestellt werden müssen. Die Wiederherstellungszeit beträgt für gewöhnlich weniger als 12 Stunden.

> [!NOTE] 
> Ist der PostgreSQL-Quellserver mit kundenseitig verwalteten Schlüsseln verschlüsselt, finden Sie in der [Dokumentation](concepts-data-encryption-postgresql.md) weitere Aspekte. 

> [!NOTE]
> Wenn Sie einen gelöschten PostgreSQL-Server wiederherstellen möchten, befolgen Sie das [hier](howto-restore-dropped-server.md) beschriebene Verfahren.

### <a name="point-in-time-restore"></a>Wiederherstellung bis zu einem bestimmten Zeitpunkt

Unabhängig von Ihrer gewählten Option für die Sicherungsredundanz können Sie eine Wiederherstellung für einen beliebigen Zeitpunkt durchführen, der innerhalb Ihres Aufbewahrungszeitraums für Sicherungen liegt. Ein neuer Server wird in derselben Azure-Region wie der ursprüngliche Server erstellt. Bei der Erstellung werden die Konfiguration für den Tarif, die Computegeneration, die Anzahl von V-Kernen, die Speichergröße, den Aufbewahrungszeitraum für Sicherungen und die Option für Sicherungsredundanz des ursprünglichen Servers verwendet.

Die Point-in-Time-Wiederherstellung ist für viele Szenarien hilfreich. Beispiele hierfür sind Fälle, in denen ein Benutzer versehentlich Daten löscht oder eine wichtige Tabelle oder Datenbank entfernt oder in denen eine Anwendung aufgrund eines Defekts fälschlicherweise Daten durch fehlerhafte Daten überschreibt.

Unter Umständen müssen Sie warten, bis die nächste Transaktionsprotokollsicherung erstellt wird, bevor Sie die Wiederherstellung für einen Zeitpunkt innerhalb der letzten fünf Minuten durchführen können.

Wenn Sie eine gelöschte Tabelle wiederherstellen möchten 
1. Stellen Sie den Quellserver mit der Zeitpunktmethode wieder her.
2. Sichern Sie die Tabelle mit `pg_dump` auf dem wiederhergestellten Server.
3. Benennen Sie die Quelltabelle auf dem ursprünglichen Server um.
4. Importieren Sie die Tabelle mit der psql-Befehlszeile auf den ursprünglichen Server.
5. Optional können Sie den wiederhergestellten Server löschen.

>[!Note]
> Es wird empfohlen, nicht mehrere Wiederherstellungen für denselben Server gleichzeitig zu erstellen. 

### <a name="geo-restore"></a>Geowiederherstellung

Sie können einen Server in einer anderen Azure-Region wiederherstellen, in der der Dienst verfügbar ist, wenn Sie Ihren Server für georedundante Sicherungen konfiguriert haben. Server, die bis zu 4 TB Speicherkapazität unterstützen, können in der geografisch gekoppelten Region oder in einer beliebigen Region wiederhergestellt werden, die bis zu 16 TB Speicherkapazität unterstützt. Für Server, die bis zu 16 TB Speicherkapazität unterstützen, können Geosicherungen auch in beliebigen Regionen wiederhergestellt werden, die Server mit 16 TB unterstützen. Eine Liste der unterstützten Regionen finden Sie unter [Azure Database for PostgreSQL-Tarife](concepts-pricing-tiers.md).

Die Geowiederherstellung ist die Standardoption für die Wiederherstellung, wenn Ihr Server aufgrund eines Incidents in der Region, in der der Server gehostet wird, nicht verfügbar ist. Wenn Ihre Datenbankanwendung wegen eines umfangreichen Incidents in einer Region nicht mehr verfügbar ist, können Sie einen Server aus den georedundanten Sicherungen auf einem Server in einer beliebigen anderen Region wiederherstellen. Zwischen der Erstellung einer Sicherung und der Replikation in einer anderen Region kommt es zu einer Verzögerung. Diese Verzögerung kann bis zu einer Stunde betragen. Folglich kann bei einem Notfall ein Datenverlust von bis zu einer Stunde auftreten.

Während der Geowiederherstellung können folgende Serverkonfigurationen geändert werden: Computegeneration, virtueller Kern, Aufbewahrungszeitraum für die Sicherung und Sicherungsredundanzoptionen. Das Ändern des Tarifs („Basic“, „Allgemein“ oder „Arbeitsspeicheroptimiert“) oder der Größe des Speichers wird nicht unterstützt.

> [!NOTE]
> Verwendet der Quellserver Infrastrukturmehrfachverschlüsselung, gelten bei der Serverwiederherstellung bestimmte Einschränkungen, u. a. in Bezug auf die verfügbaren Regionen. Weitere Informationen finden Sie unter [Azure Database for PostgreSQL: doppelte Infrastrukturverschlüsselung](concepts-infrastructure-double-encryption.md).

### <a name="perform-post-restore-tasks"></a>Durchführen der Aufgaben nach der Wiederherstellung

Nach beiden Wiederherstellungsverfahren sollten Sie die folgenden Aufgaben durchführen, um Ihre Benutzer und Anwendungen wieder in den betriebsbereiten Zustand zu versetzen:

- Umleiten von Clients und Clientanwendungen an den neuen Server, wenn der neue Server den ursprünglichen Server ersetzen soll. Ändern Sie auch den Benutzernamen in `username@new-restored-server-name`.
- Sicherstellen, dass geeignete Firewallregeln auf Serverebene und VNet-Regeln vorhanden sind, damit Benutzer eine Verbindung herstellen können Diese Regeln werden nicht vom ursprünglichen Server kopiert.
- Sicherstellen, dass geeignete Anmeldungen und Berechtigungen auf Datenbankebene vorhanden sind
- Konfigurieren der erforderlichen Warnungen

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die Wiederherstellung mithilfe des  [Azure-Portals](howto-restore-server-portal.md).
- Erfahren Sie mehr über die Wiederherstellung mithilfe der  [Azure-Befehlszeilenschnittstelle](howto-restore-server-cli.md).
- Weitere Informationen zur Geschäftskontinuität finden Sie in der  [Übersicht über die Geschäftskontinuität](concepts-business-continuity.md).