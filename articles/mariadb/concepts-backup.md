---
title: Sichern und Wiederherstellen – Azure Database for MariaDB
description: Erfahren Sie mehr über das automatische Sichern und Wiederherstellen Ihres Azure Database for MariaDB-Servers.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 8/13/2020
ms.openlocfilehash: 68605a22dd0d0b2b716b148399c8406a1ea8d89e
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541741"
---
# <a name="backup-and-restore-in-azure-database-for-mariadb"></a>Sichern und Wiederherstellen in Azure Database for MariaDB

Azure Database for MariaDB erstellt automatisch Sicherungen und speichert diese in einem lokal redundanten oder georedundanten Speicher, der von einem Benutzer konfiguriert wurde. Sicherungen können verwendet werden, um für Ihren Server den Stand zu einem bestimmten Zeitpunkt wiederherzustellen. Sicherungen und Wiederherstellungen sind wesentliche Bestandteile jeder Strategie für Geschäftskontinuität, da Ihre Daten so vor versehentlichen Beschädigungen und Löschungen geschützt werden.

## <a name="backups"></a>Backups

Azure Database for MariaDB erstellt vollständige, differenzielle und Transaktionsprotokollsicherungen. Dank dieser Sicherungen können Sie für einen Server den Stand zu einem beliebigen Zeitpunkt wiederherstellen, der innerhalb Ihres konfigurierten Aufbewahrungszeitraums für Sicherungen liegt. Die Standardaufbewahrungsdauer für Sicherungen beträgt sieben Tage. Optional können Sie einen Zeitraum von bis zu 35 Tagen festlegen. Zur Verschlüsselung aller Sicherungen wird die AES-Verschlüsselung mit 256 Bit verwendet.

Diese Sicherungsdateien sind nicht für den Benutzer verfügbar und können nicht exportiert werden. Diese Sicherungen können nur für Wiederherstellungsvorgänge in Azure Database for MariaDB verwendet werden. Zum Kopieren einer Datenbank können Sie [mysqldump](howto-migrate-dump-restore.md) verwenden.

### <a name="backup-frequency"></a>Sicherungshäufigkeit

#### <a name="servers-with-up-to-4-tb-storage"></a>Server mit bis zu 4 TB Speicher

Bei Servern, die bis zu 4 TB Speicher unterstützen, erfolgt jede Woche eine vollständige Sicherung. Differenzielle Sicherungen werden zweimal täglich ausgeführt. Transaktionsprotokollsicherungen finden alle fünf Minuten statt.

#### <a name="servers-with-up-to-16-tb-storage"></a>Server mit bis zu 16 TB Speicher
In einigen [Azure-Regionen](concepts-pricing-tiers.md#storage) unterstützen alle neu bereitgestellten Server bis zu 16 TB Speicher. Die Sicherungen auf diesen großen Speicherservern basieren auf Momentaufnahmen. Die erste vollständige Momentaufnahmensicherung wird unmittelbar nach der Erstellung des Servers eingeplant. Diese erste vollständige Momentaufnahmensicherung wird als Basissicherung des Servers beibehalten. Nachfolgende Momentaufnahmensicherungen sind nur differenzielle Sicherungen. 

Differentielle Momentaufnahmesicherungen werden mindestens einmal täglich erstellt. Differenzielle Momentaufnahmensicherungen erfolgen nicht nach einem festgelegten Zeitplan. Differenzielle Momentaufnahmesicherungen werden alle 24 Stunden ausgeführt, es sei denn, das Transaktionsprotokoll (binlog in MariaDB) überschreitet 50 GB seit der letzten differenziellen Sicherung. An einem Tag sind maximal sechs differenzielle Momentaufnahmen zulässig. 

Transaktionsprotokollsicherungen finden alle fünf Minuten statt. 

### <a name="backup-retention"></a>Sicherungsaufbewahrung

Sicherungen werden basierend auf der Einstellung für den Aufbewahrungszeitraum der Sicherung auf dem Server beibehalten. Sie können einen Aufbewahrungszeitraum von 7 bis 35 Tagen auswählen. Der Standardaufbewahrungszeitraum beträgt sieben Tage. Sie können den Aufbewahrungszeitraum bei der Servererstellung oder später festlegen, indem Sie die Sicherungskonfiguration mithilfe des [Azure-Portals](howto-restore-server-portal.md#set-backup-configuration) oder über die [Azure CLI](howto-restore-server-cli.md#set-backup-configuration) aktualisieren. 

Mit „Aufbewahrungszeit für Sicherung“ wird auch gesteuert, für welchen zurückliegenden Zeitraum eine Point-in-Time-Wiederherstellung durchgeführt werden kann, da dies auf den verfügbaren Sicherungen basiert. Der Aufbewahrungszeitraum kann auch als Wiederherstellungsfenster im Hinblick auf die Wiederherstellung behandelt werden. Alle Sicherungen, die zum Durchführen einer Zeitpunktwiederherstellung innerhalb des Aufbewahrungszeitraums für die Sicherung erforderlich sind, werden im Sicherungsspeicher beibehalten. Wenn der Aufbewahrungszeitraum für Sicherungen beispielsweise auf sieben Tage festgelegt ist, entspricht das Wiederherstellungsfenster einer Dauer von sieben Tagen. In diesem Szenario bleiben alle Sicherungen erhalten, die zum Wiederherstellen des Servers in den letzten sieben Tagen erforderlich sind. Beispiel für Sicherungsaufbewahrungsfenster von sieben Tagen:
- Bei Servern mit bis zu 4 TB Speicher werden bis zu zwei vollständige Datenbanksicherungen, alle differenziellen Sicherungen sowie Transaktionsprotokollsicherungen beibehalten, die seit der frühesten Datenbanksicherung durchgeführt wurden.
-   Bei Servern mit bis zu 16 TB Speicher werden die vollständige Datenbankmomentaufnahme, alle differenziellen Momentaufnahmen und die Transaktionsprotokollsicherungen der letzten acht Tage beibehalten.

#### <a name="long-term-retention-of-backups"></a>Langzeitaufbewahrung von Sicherungen
Eine Langzeitaufbewahrung von Sicherungen (mehr als 35 Tage) wird vom Dienst derzeit noch nicht nativ unterstützt. Sie können aber „mysqldump“ verwenden, um Sicherungen zu erstellen und für die langfristige Aufbewahrung zu speichern. Dies wird von unserem Supportteam in einem [Artikel mit Schrittanleitungen](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/automate-backups-of-your-azure-database-for-mysql-server-to/ba-p/1791157) beschrieben. 

### <a name="backup-redundancy-options"></a>Optionen für Sicherungsredundanz

Mit Azure Database for MariaDB können Sie in den Tarifen „Allgemein“ und „Arbeitsspeicheroptimiert“ flexibel zwischen lokal redundantem und georedundantem Sicherungsspeicher wählen. Wenn die Sicherungen in einem georedundanten Sicherungsspeicher gespeichert werden, werden sie nicht nur in der Region vorgehalten, in der Ihr Server gehostet wird. Sie werden außerdem in einem [gekoppelten Datencenter](../best-practices-availability-paired-regions.md) repliziert. Dies erhöht den Schutz und ermöglicht in einem Notfall die Wiederherstellung Ihres Servers in einer anderen Region. Im Tarif „Basic“ ist nur lokal redundanter Sicherungsspeicher verfügbar.

#### <a name="moving-from-locally-redundant-to-geo-redundant-backup-storage"></a>Wechseln von lokal redundantem zu georedundantem Sicherungsspeicher
Das Konfigurieren von lokal redundantem oder georedundantem Speicher für die Sicherung ist nur während der Erstellung des Servers zulässig. Nachdem der Server bereitgestellt wurde, können Sie die Option für die Sicherungsspeicherredundanz nicht mehr ändern. Wenn Sie Ihren Sicherungsspeicher von lokal redundantem Speicher auf georedundanten Speicher umstellen möchten, ist das Erstellen eines neuen Servers und Migrieren der Daten mithilfe von [Sicherungen und Wiederherstellungen](howto-migrate-dump-restore.md) die einzige unterstützte Option.

### <a name="backup-storage-cost"></a>Kosten für Sicherungsspeicher

Azure Database for MariaDB stellt bis zu 100 % Ihres bereitgestellten Serverspeichers ohne zusätzliche Kosten als Sicherungsspeicher zur Verfügung. Wenn zusätzlicher Sicherungsspeicher verwendet wird, wird dies in GB pro Monat berechnet. Beispiel: Wenn Sie einen Server mit 250 GB bereitgestellt haben, verfügen Sie über 250 GB an zusätzlichem Speicher, der ohne zusätzliche Kosten für Serversicherungen zur Verfügung steht. Der für Sicherungen verwendete Speicher über 250 GB wird gemäß dem [Preismodell](https://azure.microsoft.com/pricing/details/mariadb/) abgerechnet. 

Sie können die im Azure-Portal in Azure Monitor verfügbare Metrik für den [verwendeten Sicherungsspeicher](concepts-monitoring.md) zum Überwachen des von einem Server genutzten Sicherungsspeichers verwenden. Die Metrik für den verwendeten Sicherungsspeicher stellt den gesamten Speicherplatz dar, der von allen vollständigen Datenbanksicherungen, differenziellen Sicherungen und Protokollsicherungen beansprucht wurde, die auf der Grundlage des für den Server festgelegten Aufbewahrungszeitraums für Sicherungen aufbewahrt wurden. Die Häufigkeit der Sicherungen wird durch den Dienst verwaltet und wurde bereits erläutert. Eine hohe Transaktionsaktivität auf dem Server kann dazu führen, dass die Sicherungsspeicherauslastung unabhängig von der Gesamtgröße der Datenbank zunimmt. Bei georedundantem Speicher wird doppelt so viel Sicherungsspeicher genutzt wie bei lokal redundantem Speicher. 

Das primäre Mittel zum Steuern der Sicherungsspeicherkosten besteht darin, den geeigneten Aufbewahrungszeitraum festzulegen und die richtige Sicherungsredundanzoptionen auszuwählen, um die gewünschten Wiederherstellungsziele zu erreichen. Sie können als Aufbewahrungszeitraum einen Bereich von 7 bis 35 Tagen auswählen. Bei universellen und arbeitsspeicheroptimierten Servern können Sie georedundanten Speicher für Sicherungen auswählen.

## <a name="restore"></a>Restore

Wenn in Azure Database for MariaDB eine Wiederherstellung ausgeführt wird, wird aus den Sicherungen des ursprünglichen Servers ein neuer Server erstellt und alle auf dem Server befindlichen Datenbanken werden wiederhergestellt.

Es gibt zwei Arten der Wiederherstellung:

- Die **Point-in-Time-Wiederherstellung** ist für beide Sicherungsredundanzoptionen verfügbar. Es wird unter Verwendung einer Kombination aus vollständigen Sicherungen und Transaktionsprotokollsicherungen in der Region des ursprünglichen Servers ein neuer Server erstellt.
- Die **Geowiederherstellung** ist nur verfügbar, wenn Sie Ihren Server für georedundanten Speicher konfiguriert haben. Hierbei können Sie den Server unter Verwendung der aktuellsten Sicherung in einer anderen Region wiederherstellen.

Die geschätzte Wiederherstellungszeit hängt von verschiedenen Faktoren ab, z.B. der Datenbankgröße, Transaktionsprotokollgröße und Netzwerkbandbreite sowie der Gesamtzahl von Datenbanken, die gleichzeitig in derselben Region wiederhergestellt werden müssen. Die Wiederherstellungszeit beträgt für gewöhnlich weniger als 12 Stunden.

> [!IMPORTANT]
> Gelöschte Server **können nicht** wiederhergestellt werden. Wenn Sie den Server löschen, werden alle zum Server gehörigen Datenbanken ebenfalls gelöscht und können nicht wiederhergestellt werden. Um Serverressourcen nach der Bereitstellung vor versehentlichem Löschen oder unerwarteten Änderungen zu schützen, können Administratoren [Verwaltungssperren](../azure-resource-manager/management/lock-resources.md) nutzen.

### <a name="point-in-time-restore"></a>Wiederherstellung bis zu einem bestimmten Zeitpunkt

Unabhängig von Ihrer gewählten Option für die Sicherungsredundanz können Sie eine Wiederherstellung für einen beliebigen Zeitpunkt durchführen, der innerhalb Ihres Aufbewahrungszeitraums für Sicherungen liegt. Ein neuer Server wird in derselben Azure-Region wie der ursprüngliche Server erstellt. Bei der Erstellung werden die Konfiguration für den Tarif, die Computegeneration, die Anzahl von V-Kernen, die Speichergröße, den Aufbewahrungszeitraum für Sicherungen und die Option für Sicherungsredundanz des ursprünglichen Servers verwendet.

Die Point-in-Time-Wiederherstellung ist für viele Szenarien hilfreich. Beispiele hierfür sind Fälle, in denen ein Benutzer versehentlich Daten löscht oder eine wichtige Tabelle oder Datenbank entfernt oder in denen eine Anwendung aufgrund eines Defekts fälschlicherweise Daten durch fehlerhafte Daten überschreibt.

Unter Umständen müssen Sie warten, bis die nächste Transaktionsprotokollsicherung erstellt wird, bevor Sie die Wiederherstellung für einen Zeitpunkt innerhalb der letzten fünf Minuten durchführen können.

### <a name="geo-restore"></a>Geowiederherstellung

Sie können einen Server in einer anderen Azure-Region wiederherstellen, in der der Dienst verfügbar ist, wenn Sie Ihren Server für georedundante Sicherungen konfiguriert haben. Server, die bis zu 4 TB Speicherkapazität unterstützen, können in der geografisch gekoppelten Region oder in einer beliebigen Region wiederhergestellt werden, die bis zu 16 TB Speicherkapazität unterstützt. Für Server, die bis zu 16 TB Speicherkapazität unterstützen, können Geosicherungen auch in beliebigen Regionen wiederhergestellt werden, die Server mit 16 TB unterstützen. Eine Liste der unterstützten Regionen finden Sie unter [Azure Database for MariaDB-Tarife](concepts-pricing-tiers.md).

Die Geowiederherstellung ist die Standardoption für die Wiederherstellung, wenn Ihr Server aufgrund eines Incidents in der Region, in der der Server gehostet wird, nicht verfügbar ist. Wenn Ihre Datenbankanwendung wegen eines umfangreichen Incidents in einer Region nicht mehr verfügbar ist, können Sie einen Server aus den georedundanten Sicherungen auf einem Server in einer beliebigen anderen Region wiederherstellen. Bei der Geowiederherstellung wird die aktuellste Sicherung des Servers verwendet. Zwischen der Erstellung einer Sicherung und der Replikation in einer anderen Region kommt es zu einer Verzögerung. Diese Verzögerung kann bis zu einer Stunde betragen. Folglich kann bei einem Notfall ein Datenverlust von bis zu einer Stunde auftreten.

> [!IMPORTANT]
>Wenn für einen neu erstellten Server eine Geowiederherstellung durchgeführt wird, kann die anfängliche Sicherungssynchronisierung je nach Datenumfang mehr als 24 Stunden dauern, da der Kopiervorgang einer ersten vollständigen Momentaufnahmesicherung sehr viel länger dauert. Nachfolgende Momentaufnahmesicherungen sind inkrementelle Kopien, daher erfolgen nachfolgende Wiederherstellungen 24 Stunden nach der Servererstellung schneller. Wenn Sie Geowiederherstellungen zur Definition Ihrer RTO auswerten, sollten Sie die Geowiederherstellung bei neuen Servern **erst 24 Stunden** nach der Servererstellung auswerten, um bessere Schätzwerte zu erhalten.

Während der Geowiederherstellung können folgende Serverkonfigurationen geändert werden: Computegeneration, virtueller Kern, Aufbewahrungszeitraum für die Sicherung und Sicherungsredundanzoptionen. Allerdings wird während der Geowiederherstellung nicht das Ändern des Tarifs („Basic“, „Allgemein“ oder „Arbeitsspeicheroptimiert“) oder der Speichergröße unterstützt.

Die geschätzte Wiederherstellungszeit hängt von verschiedenen Faktoren ab, z.B. der Datenbankgröße, Transaktionsprotokollgröße und Netzwerkbandbreite sowie der Gesamtzahl von Datenbanken, die gleichzeitig in derselben Region wiederhergestellt werden müssen. Die Wiederherstellungszeit beträgt für gewöhnlich weniger als 12 Stunden.

### <a name="perform-post-restore-tasks"></a>Durchführen der Aufgaben nach der Wiederherstellung

Nach beiden Wiederherstellungsverfahren sollten Sie die folgenden Aufgaben durchführen, um Ihre Benutzer und Anwendungen wieder in den betriebsbereiten Zustand zu versetzen:

- Umleiten von Clients und Clientanwendungen an den neuen Server, wenn der neue Server den ursprünglichen Server ersetzen soll
- Stellen Sie sicher, dass geeignete VNET-Regeln vorhanden sind, damit Benutzer eine Verbindung herstellen können. Diese Regeln werden nicht vom ursprünglichen Server kopiert.
- Sicherstellen, dass geeignete Anmeldungen und Berechtigungen auf Datenbankebene vorhanden sind
- Konfigurieren der erforderlichen Warnungen

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Geschäftskontinuität finden Sie in der  [Übersicht über die Geschäftskontinuität](concepts-business-continuity.md).
- Informationen zur Wiederherstellung des Zustands zu einem bestimmten Zeitpunkt über das Azure-Portal finden Sie unter  [Sichern und Wiederherstellen eines Servers in Azure Database for MySQL mit dem Azure-Portal](howto-restore-server-portal.md).
- Informationen zur Wiederherstellung des Zustands zu einem bestimmten Zeitpunkt über die Azure-Befehlszeilenschnittstelle finden Sie unter  [Sichern und Wiederherstellen eines Servers in Azure Database for MySQL mit der Azure CLI](howto-restore-server-cli.md).
