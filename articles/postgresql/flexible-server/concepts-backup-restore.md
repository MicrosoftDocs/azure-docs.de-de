---
title: Sicherung und Wiederherstellung in Azure Database for PostgreSQL – Flexible Server
description: Erfahren Sie mehr über die Konzepte zur Sicherung und Wiederherstellung für Azure Database for PostgreSQL – Flexible Server
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: d0e79e42c7c004638336ada23de663bbe74b7e48
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92532644"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---flexible-server"></a>Sicherung und Wiederherstellung in Azure Database for PostgreSQL – Flexible Server

> [!IMPORTANT]
> Azure Database for PostgreSQL – Flexible Server befindet sich in der Vorschau.

Sicherungen sind ein wesentlicher Teil jeder Strategie für Geschäftskontinuität. Sie tragen zum Schutz von Daten vor versehentlicher Beschädigung oder Löschung bei. Azure Database for PostgreSQL – Flexible Server sichert Ihren Server automatisch und bewahrt die Sicherungen für eine Dauer von bis zu 35 Tagen auf. Während der Wiederherstellung können Sie Datum und Uhrzeit für die Wiederherstellung innerhalb des Aufbewahrungszeitraums angeben. Die Gesamtzeit der Wiederherstellung hängt von der Größe der Datenbankdateien und dem Umfang der durchzuführenden Wiederherstellung ab. 

### <a name="backup-process-in-flexible-server"></a>Sicherungsprozess in der Flexible Server-Instanz
Die erste Momentaufnahmensicherung wird unmittelbar nach Erstellung der Flexible Server-Instanz geplant. Anschließend erfolgt eine tägliche Momentaufnahmensicherung der Datendateien. Sicherungen werden in einem zonenredundanten Speicher innerhalb einer Region gespeichert. Transaktionsprotokolle (Write-Ahead-Protokolle, WAL) werden auch fortlaufend archiviert, damit eine Wiederherstellung bis zur letzten Transaktion möglich ist, für die ein Commit erfolgt ist. Dank dieser Daten- und Protokollsicherungen können Sie für einen Server den Stand zu einem beliebigen Zeitpunkt wiederherstellen, der innerhalb Ihres konfigurierten Aufbewahrungszeitraums für Sicherungen liegt. Zur Verschlüsselung aller Sicherungen wird die AES-Verschlüsselung mit 256 Bit verwendet.

Wenn die Datenbank mit Hochverfügbarkeit konfiguriert ist, werden auf dem primären Server tägliche Momentaufnahmen erstellt. Auf dem Standbyserver werden kontinuierliche Protokollsicherungen durchgeführt.

> [!IMPORTANT]
>Auf angehaltenen Servern werden keine Sicherungen durchgeführt. Die Sicherungen werden jedoch fortgesetzt, wenn die Datenbank entweder automatisch nach 7 Tagen oder vom Benutzer gestartet wird.

Die Sicherungen können nur für Wiederherstellungsvorgänge in der Flexible Server-Instanz verwendet werden. Wenn Sie Daten vom flexiblen Server exportieren oder auf diesem importieren möchten, befolgen Sie die Methodik zur [Sicherung und Wiederherstellung](../howto-migrate-using-dump-and-restore.md).


### <a name="backup-retention"></a>die Aufbewahrung der Sicherung

Sicherungen werden basierend auf der Einstellung für den Aufbewahrungszeitraum der Sicherung für den Server aufbewahrt. Sie können einen Aufbewahrungszeitraum von 7 bis 35 Tagen auswählen. Der Standardaufbewahrungszeitraum ist 7 Tage. Sie können den Aufbewahrungszeitraum bei der Servererstellung festlegen oder ihn zu einem späteren Zeitpunkt ändern. Sicherungen werden auch für angehaltene Server aufbewahrt.

Der Aufbewahrungszeit für Sicherung bestimmt auch, für welchen zurückliegenden Zeitraum eine Zeitpunktwiederherstellung erfolgen kann, da dieser auf verfügbaren Sicherungen basiert. Der Aufbewahrungszeitraum kann auch als Wiederherstellungsfenster im Hinblick auf die Wiederherstellung behandelt werden. Alle Sicherungen, die zum Durchführen einer Zeitpunktwiederherstellung innerhalb des Aufbewahrungszeitraums für die Sicherung erforderlich sind, werden im Sicherungsspeicher aufbewahrt. Wenn der Aufbewahrungszeitraum für Sicherungen z. B. auf sieben Tage festgelegt ist, entspricht das Wiederherstellungsfenster den letzten sieben Tagen. In diesem Szenario werden alle Sicherungen und Protokolle aufbewahrt, die zum Wiederherstellen des Servers in den letzten sieben Tagen erforderlich sind. 


### <a name="backup-storage-cost"></a>Kosten für Sicherungsspeicher

Für die Flexible Server-Instanz werden bis zu 100 % Ihres bereitgestellten Serverspeichers ohne zusätzliche Kosten als Sicherungsspeicher zur Verfügung gestellt. Wenn zusätzlicher Sicherungsspeicher verwendet wird, wird dies in GB pro Monat berechnet. Wenn Sie beispielsweise einen Server mit 250 GiB Speicherkapazität bereitgestellt haben, stehen Ihnen 250 GiB Sicherungsspeicher ohne zusätzliche Kosten zur Verfügung. Wenn die Größe der tägliche Sicherung 25 GiB beträgt, steht Ihnen kostenloser Sicherungsspeicher für bis zu 10 Tage bereit. Die Nutzung des Sicherungsspeichers über 250 GiB hinaus wird Ihnen gemäß dem [Preismodell](https://azure.microsoft.com/pricing/details/postgresql/) in Rechnung gestellt.

Sie können die im Azure-Portal verfügbare Metrik für den [verwendeten Sicherungsspeicher](../concepts-monitoring.md) zum Überwachen des von einem Server belegten Sicherungsspeichers verwenden. Die Metrik für den belegten Sicherungsspeicher stellt den gesamten Speicherplatz dar, der von allen Datenbank- und Protokollsicherungen beansprucht wurde, die auf Grundlage des für den Server festgelegten Aufbewahrungszeitraums für Sicherungen aufbewahrt wurden.  Eine hohe Transaktionsaktivität auf dem Server kann dazu führen, dass die Sicherungsspeicherauslastung unabhängig von der Gesamtgröße der Datenbank zunimmt.

Das primäre Mittel zum Steuern der Sicherungsspeicherkosten besteht darin, den geeigneten Aufbewahrungszeitraum festzulegen und die richtige Sicherungsredundanzoptionen auszuwählen, um die gewünschten Wiederherstellungsziele zu erreichen.

> [!IMPORTANT]
> Georedundante Sicherungen werden derzeit für Flexible Server-Instanzen nicht unterstützt.

## <a name="point-in-time-restore-overview"></a>Übersicht für die Zeitpunktwiederherstellung

In einer Flexible Server-Instanz wird bei der Zeitpunktwiederherstellung ein neuer Server aus den Sicherungen der Flexible Server-Instanz in derselben Region wie Ihr Quellserver erstellt. Bei der Erstellung wird die Konfiguration für den Tarif, die Computegeneration, Anzahl virtueller Kerne, Speichergröße, den Aufbewahrungszeitraum für Sicherungen und die Option für Sicherungsredundanz des Quellservers verwendet. Außerdem werden Tags und Einstellungen wie VNET- und Firewalleinstellungen vom Quellserver übernommen. 

 > [!IMPORTANT]
> Wenn Sie eine Flexible Server-Instanz wiederherstellen, die mit zonenredundanter Hochverfügbarkeit konfiguriert ist, wird der wiederhergestellte Server ohne Hochverfügbarkeit und in der gleichen Region wie Ihr primärer Server konfiguriert. 

 ### <a name="restore-process"></a>Wiederherstellungsvorgang

Die physischen Datenbankdateien werden zunächst aus den Momentaufnahmensicherungen am Datenspeicherort des Servers wiederhergestellt. Die entsprechende Sicherung, die vor dem gewünschten Zeitpunkt erstellt wurde, wird automatisch ausgewählt und wiederhergestellt. Anschließend wird ein Wiederherstellungsprozess unter Verwendung von WAL-Dateien eingeleitet, um die Datenbank in einen konsistenten Zustand zu bringen. 

 Angenommen, die Sicherungen werden nachts um 23:00 Uhr durchgeführt. Wenn der Wiederherstellungspunkt der 15. August 2020 um 10:00 Uhr ist, wird die tägliche Sicherung vom 14. August 2020 wiederhergestellt. Die Datenbank wird bis am 25. August 2020 um 10:00 Uhr unter Verwendung der Transaktionsprotokollsicherung zwischen dem 24. August 23:00 Uhr und dem 25. August 10:00 Uhr wiederhergestellt. 

 Weitere Informationen zum Wiederherstellen des Datenbankservers finden Sie [hier](./how-to-restore-server-portal.md).

> [!IMPORTANT]
> Bei Wiederherstellungsvorgängen in der Flexible Server-Instanz wird ein neuer Datenbankserver erstellt und der vorhandene Datenbankserver nicht überschrieben.

Die Point-in-Time-Wiederherstellung ist für viele Szenarien hilfreich. Beispiele hierfür sind Fälle, in denen ein Benutzer versehentlich Daten löscht oder eine wichtige Tabelle oder Datenbank entfernt oder in denen eine Anwendung aufgrund eines Defekts fälschlicherweise Daten durch fehlerhafte Daten überschreibt. Dank der fortlaufenden Sicherung von Transaktionsprotokollen können Sie die letzte Transaktion wiederherstellen.

Sie können zwischen einem frühesten und einem benutzerdefinierten Wiederherstellungspunkt wählen.

-   **Frühester Wiederherstellungspunkt** : Je nach Aufbewahrungszeitraum ist dies der früheste Zeitpunkt, den Sie wiederherstellen können. Der älteste Sicherungszeitpunkt wird automatisch ausgewählt und im Portal angezeigt. Dies ist nützlich, wenn Sie ab diesem Zeitpunkt Untersuchungen oder Tests durchführen möchten.

-   **Benutzerdefinierter Wiederherstellungspunkt** : Mithilfe dieser Option können Sie einen beliebigen Zeitpunkt innerhalb des für diese Flexible Server-Instanz definierten Aufbewahrungszeitraums wählen. Standardmäßig wird der späteste Zeitpunkt in UTC automatisch ausgewählt, was nützlich ist, wenn Sie zu Testzwecken die letzte Transaktion wiederherstellen möchten, für die ein Commit durchgeführt wurde. Sie können optional auch andere Tage und Uhrzeiten wählen. 

Die geschätzte Wiederherstellungsdauer hängt von verschiedenen Faktoren ab, z. B. Datenbankgröße, Umfang der zu verarbeitenden Transaktionsprotokolle, Netzwerkbandbreite und der Gesamtanzahl der Datenbanken, die gleichzeitig in derselben Region wiederhergestellt werden müssen. Die gesamte Wiederherstellungsdauer beträgt normalerweise mehrere Minuten bis zu mehrere Stunden.


> [!IMPORTANT]
> Gelöschte Server **können nicht** wiederhergestellt werden. Wenn Sie den Server löschen, werden auch alle Datenbanken gelöscht, die zum Server gehören, und können nicht wiederhergestellt werden. Um Serverressourcen nach der Bereitstellung vor versehentlichem Löschen oder unerwarteten Änderungen zu schützen, können Administratoren [Verwaltungssperren](../../azure-resource-manager/management/lock-resources.md) nutzen.

## <a name="perform-post-restore-tasks"></a>Durchführen der Aufgaben nach der Wiederherstellung

Nach der Wiederherstellung der Datenbank können Sie die folgenden Aufgaben durchführen, damit Ihre Benutzer und Anwendungen wieder den Betrieb aufnehmen können:

-   Umleiten von Clients und Clientanwendungen an den neuen Server, wenn der neue Server den ursprünglichen Server ersetzen soll.

-   Sicherstellen, dass geeignete Firewallregeln auf Serverebene und VNet-Regeln vorhanden sind, damit Benutzer eine Verbindung herstellen können Diese Regeln werden nicht vom ursprünglichen Server kopiert.
  
-   Die Computekapazität des wiederhergestellten Servers kann nach Bedarf hoch- oder herunterskaliert werden.

-   Sicherstellen, dass geeignete Anmeldungen und Berechtigungen auf Datenbankebene vorhanden sind.

-   Konfigurieren der erforderlichen Warnungen.
  
-  Wenn Sie die mit Hochverfügbarkeit konfigurierte Datenbank wiederhergestellt haben und den wiederhergestellten Server mit Hochverfügbarkeit konfigurieren möchten, können Sie [diese Schritte](./how-to-manage-high-availability-portal.md) befolgen.


## <a name="next-steps"></a>Nächste Schritte

-   Weitere Informationen zur [Geschäftskontinuität](./concepts-business-continuity.md)
-   Weitere Informationen zur [zonenredundanten Hochverfügbarkeit](./concepts-high-availability.md)
-   Weitere Informationen zur [Wiederherstellung](./how-to-restore-server-portal.md)