---
title: Übersicht über die Geschäftskontinuität mit Azure Database for PostgreSQL – Flexible Server
description: Erfahren Sie mehr über die Konzepte der Geschäftskontinuität mit Azure Database for PostgreSQL – Flexible Server
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 341d3c6f0a2d84c2b7b725959db9f7e5dd80d3ff
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542963"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---flexible-server"></a>Übersicht über die Geschäftskontinuität mit Azure Database for PostgreSQL – Flexible Server

> [!IMPORTANT]
> Azure Database for PostgreSQL – Flexible Server befindet sich in der Vorschau.

**Geschäftskontinuität** in Azure Database for PostgreSQL – Flexible Server bezieht sich auf die Mechanismen, Richtlinien und Verfahren, die es Ihrem Unternehmen ermöglichen, angesichts von Störungen, insbesondere in der Computerinfrastruktur, weiter zu arbeiten. In den meisten Fällen behandelt der flexible Server die Störungen, die in der Cloudumgebung auftreten können, und hält Ihre Anwendungen und Geschäftsprozesse am Laufen. Es gibt jedoch einige Ereignisse, die nicht automatisch behandelt werden können, wie z. B.:

- Ein Benutzer löscht oder aktualisiert versehentlich eine Zeile in einer Tabelle.
- Ein Erdbeben verursacht einen Stromausfall und deaktiviert das Rechenzentrum oder eine Verfügbarkeitszone vorübergehend.
- Erforderliches Datenbankpatching, um einen Fehler oder ein Sicherheitsproblem zu beheben.

Flexible Server bietet Features, die Daten schützen und Downtimes für Ihre unternehmenskritischen Datenbanken im Fall geplanter und ungeplanter Downtimes reduzieren. Auf der Azure-Infrastruktur aufbauend, die bereits robuste Resilienz und Verfügbarkeit bietet, stellt Flexible Server Funktionen für die Geschäftskontinuität bereit, die zusätzlichen Fehlerschutz bieten, Anforderungen an die Wiederherstellungszeit erfüllen und die Datenverlustgefahr verringern. Wenn Sie die Architektur Ihrer Anwendungen entwerfen, sollten Sie die Downtimetoleranz berücksichtigen, wobei es sich um die Recovery Time Objective (RTO) und die Datenverlustgefahr handelt, die die Recovery Point Objective (RPO) ist. Beispielsweise erfordert Ihre unternehmenskritische Datenbank wesentlich strengere Anforderungen an die Uptime im Vergleich zu einer Testdatenbank.  

> [!IMPORTANT]
> Eine Vereinbarung zum Service Level (SLA) für die Uptime in % wird während der Vorschauphase nicht angeboten. 

Die nachstehende Tabelle veranschaulicht die Features, die Flexible Server bietet.


| **Feature** | **Beschreibung** | **Überlegungen** |
| ---------- | ----------- | ------------ |
| **Automatische Sicherungen** | Flexible Server führt automatisch tägliche Sicherungen Ihrer Datenbankdateien durch und sichert kontinuierlich Transaktionsprotokolle. Sicherungen können zwischen 7 Tagen und 35 Tagen aufbewahrt werden. Sie können Ihren Datenbankserver zu jedem beliebigen Zeitpunkt innerhalb des Aufbewahrungszeitraums Ihrer Sicherung wiederherstellen. RTO hängt von der Größe der wiederherzustellenden Daten und der Zeit für die Durchführung der Protokollwiederherstellung ab. Der Wert kann zwischen wenigen Minuten und 12 Stunden liegen. Weitere ausführliche Informationen finden Sie unter [Konzepte: Sicherung und Wiederherstellung](./concepts-backup-restore.md). |Sicherungsdaten verbleiben innerhalb der Region. |
| **Zonenredundante Hochverfügbarkeit** | Flexible Server kann mit zonenredundanter Hochverfügbarkeitskonfiguration bereitgestellt werden, bei der primäre und Standbyserver in zwei verschiedenen Verfügbarkeitszonen innerhalb einer Region bereitgestellt werden. Diese Hochverfügbarkeitskonfiguration schützt Ihre Datenbank vor Fehlern auf Zonenebene und hilft auch bei der Reduzierung der Downtime von Anwendungen während geplanter und ungeplanter Downtimeereignisse. Die Daten vom primären Server werden im synchronen Modus auf das Standbyreplikat repliziert. Im Falle einer Unterbrechung des primären Servers erfolgt automatisch ein Failover für den Server auf das Standbyreplikat. In den meisten Fällen wird eine RTO von 60 s bis 120 s erwartet. Es wird erwartet, dass RPO den Wert 0 (kein Datenverlust) hat. Weitere Informationen finden Sie unter [Konzepte: Hochverfügbarkeit](./concepts-high-availability.md). | Unterstützt auf den Computeebenen „Allgemeiner Zweck“ und „Arbeitsspeicheroptimiert“. Nur verfügbar in Regionen, in denen mehrere Zonen verfügbar sind. |
| **Managed Disks Premium** | Datenbankdateien werden in einem sehr langlebigen und zuverlässigen verwalteten Premiumspeicher gespeichert. Dies bietet Datenredundanz mit drei Replikatkopien, die in einer Verfügbarkeitszone mit automatischer Datenwiederherstellung gespeichert sind. Weitere Informationen finden Sie in der [Dokumentation zu verwalteten Datenträgern](../../virtual-machines/managed-disks-overview.md). | In einer Verfügbarkeitszone gespeicherte Daten. |
| **Zonenredundante Sicherung** | Flexible Server-Sicherungen werden automatisch und sicher in einem zonenredundanten Speicher innerhalb einer Region gespeichert. Während eines Fehlers auf Ebene der Zone, in der Ihr Server bereitgestellt ist, und wenn Ihr Server nicht mit Zonenredundanz konfiguriert ist, können Sie Ihre Datenbank dennoch weiterhin mithilfe des letzten Wiederherstellungspunkts in einer anderen Zone wiederherstellen. Weitere Informationen finden Sie unter [Konzepte: Sicherung und Wiederherstellung](./concepts-backup-restore.md).| Nur gültig in Regionen, in denen mehrere Zonen verfügbar sind.|


## <a name="planned-downtime-events"></a>Geplante Downtimeereignisse
Im Folgenden finden Sie einige Szenarien mit geplanter Wartung. Diese Ereignisse verursachen in der Regel bis zu wenige Minuten Downtime, und diese ohne Datenverlust.

| **Szenario** | **Process**|
| ------------------- | ----------- | 
| <b>Computeskalierung (vom Benutzer initiiert)| Während des Computeskalierungsvorgangs können aktive Prüfpunkte abgeschlossen werden, Clientverbindungen werden entladen, alle Transaktionen ohne Commit werden abgebrochen, Speicher wird getrennt, und der Server wird anschließend heruntergefahren. Ein neuer flexibler Server mit demselben Datenbankservernamen wird mithilfe der skalierten Computekonfiguration bereitgestellt. Der Speicher wird dann an den neuen Server angefügt und die Datenbank gestartet, die bei Bedarf eine Wiederherstellung durchführt, bevor sie Clientverbindungen akzeptiert. |
| <b>Hochskalieren des Speichers (vom Benutzer initiiert) | Wenn ein Hochskalierungsvorgang initiiert wird, können aktive Prüfpunkte abgeschlossen werden, Clientverbindungen werden entladen, alle Transaktionen ohne Commit werden abgebrochen, und der Server wird anschließend heruntergefahren. Der Speicher wird auf die gewünschte Größe skaliert und dann an den neuen Server angefügt. Bei Bedarf wird eine Wiederherstellung ausgeführt, bevor Clientverbindungen akzeptiert werden. Beachten Sie, dass die Herunterskalierung der Speichergröße nicht unterstützt wird. |
| <b>Bereitstellung neuer Software (von Azure initiiert) | Das Rollout neuer Features oder die Behebung von Fehlern erfolgt automatisch als Teil der geplanten Wartungsarbeiten des Diensts, und Sie können planen, wann diese Aktivitäten stattfinden sollen. Weitere Informationen finden Sie in Ihrem [Portal](https://aka.ms/servicehealthpm). | 
| <b>Upgrades auf Nebenversionen (von Azure initiiert) | Azure Database for PostgreSQL patcht Datenbankserver automatisch auf die von Azure festgelegte Nebenversion. Dies erfolgt im Rahmen der geplanten Wartung des Diensts. Der Datenbankserver wird automatisch mit der neuen Nebenversion neu gestartet. Weitere Informationen finden Sie in der [Dokumentation](../concepts-monitoring.md#planned-maintenance-notification). Sie können auch in Ihrem [Portal](https://aka.ms/servicehealthpm) nachsehen.| 

 Wenn der flexible Server mit **zonenredundanter Hochverfügbarkeit** konfiguriert ist, führt der flexible Server die Skalierungs- und Wartungsvorgänge zuerst auf dem Standbyserver durch. Weitere Informationen finden Sie unter [Konzepte: Hochverfügbarkeit](./concepts-high-availability.md).

##  <a name="unplanned-downtime-mitigation"></a>Minimierung von ungeplanter Downtime

Ungeplante Downtimes können aufgrund von unvorhergesehenen Unterbrechungen auftreten, darunter Fehler in der zugrunde liegenden Hardware, Netzwerkprobleme und Softwarefehler. Wenn der mit Hochverfügbarkeit konfigurierte Datenbankserver unerwartet ausfällt, wird das Standbyreplikat aktiviert, und die Clients können ihren Betrieb fortsetzen. Wenn er nicht mit Hochverfügbarkeit konfiguriert wurde, wird bei Fehlschlagen des Neustartversuchs automatisch ein neuer Datenbankserver bereitgestellt. Ungeplante Downtime lässt sich zwar nicht vollständig vermeiden, wird aber vom flexiblen Server durch automatisches Ausführen von Wiederherstellungsvorgängen minimiert, ohne dass ein Eingreifen durch einen Benutzer erforderlich ist. 
   
### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Ungeplante Downtime: Fehlerszenarios und Dienstwiederherstellung
Im Folgenden finden Sie einige Szenarien mit ungeplanten Fehlern und dem zugehörigen Wiederherstellungsprozess. 

| **Szenario** | **Wiederherstellungsprozess [ohne Hochverfügbarkeit]** | **Wiederherstellungsprozess [mit Hochverfügbarkeit]** |
| ---------- | ---------- | ------- |
| <B>Ausfall des Datenbankservers | Wenn der Datenbankserver nicht verfügbar ist, versucht Azure, den Datenbankserver neu zu starten. Wenn dabei ein Fehler auftritt, wird der Datenbankserver auf einem anderen physischen Knoten neu gestartet.  <br /> <br /> Die Wiederherstellungszeit (Recovery Time Objective, RTO) hängt von verschiedenen Faktoren ab, z. B. der Aktivität zum Zeitpunkt des Fehlers. Zu diesen Aktivitäten zählen große Transaktionen und das Volume der Wiederherstellungsvorgänge, die während des Starts des Datenbankservers ausgeführt wurden. <br /> <br /> Anwendungen, die die PostgreSQL-Datenbanken verwenden, müssen so konzipiert sein, dass sie getrennte Verbindungen und Transaktionsfehler erkennen und entsprechende Wiederholungsversuche durchführen. | Wenn der Datenbankserverfehler erkannt wird, wird ein Failover des Servers auf den Standbyserver durchgeführt, wodurch die Downtime verringert wird. Weitere Informationen finden Sie auf der [Seite „Hochverfügbarkeitskonzepte“](./concepts-high-availability.md). Es wird erwartet, dass RTO einen Wert von 60–120 s hat, ohne Datenverlust. |
| <B>Speicherfehler | Speicherbezogene Probleme wie der Ausfall eines Datenträgers oder physische Blockbeschädigungen haben keine Auswirkungen auf Anwendungen. Da drei Kopien der Daten gespeichert werden, wird eine Kopie der Daten vom verbleibenden Speicher bereitgestellt. Der beschädigte Datenblock wird automatisch repariert, und eine neue Kopie der Daten wird automatisch erstellt. | Bei seltenen oder nicht behebbaren Fehlern wie der Unmöglichkeit, auf den gesamten Speicher zuzugreifen, erfolgt ein Failover des flexiblen Servers auf das Standbyreplikat, um die Downtime zu reduzieren. Weitere Informationen finden Sie auf der [Seite „Hochverfügbarkeitskonzepte“](./concepts-high-availability.md). |
| <b> Logische Fehler/Benutzerfehler | Zur Wiederherstellung nach Benutzerfehlern, z. B. versehentlich gelöschte Tabellen oder fehlerhaft aktualisierte Daten, müssen Sie eine [Zeitpunktwiederherstellung](../concepts-backup.md) (Point-in-Time Recovery, PITR) ausführen. Während der Ausführung des Wiederherstellungsvorgangs geben Sie den benutzerdefinierten Wiederherstellungspunkt an, bei dem es sich um den Zeitpunkt direkt vor dem Auftreten des Fehlers handelt.<br> <br>  Wenn Sie anstelle aller Datenbanken auf dem Datenbankserver nur eine Teilmenge von Datenbanken oder bestimmte Tabellen wiederherstellen möchten, können Sie den Datenbankserver in einer neuen Instanz wiederherstellen, die Tabelle(n) über [pg_dump](https://www.postgresql.org/docs/11/app-pgdump.html) exportieren und sie dann über [pg_restore](https://www.postgresql.org/docs/11/app-pgrestore.html) in Ihrer Datenbank wiederherstellen. | Diese Benutzerfehler sind nicht durch Hochverfügbarkeit geschützt, da alle Änderungen synchron auf den Standbyserver repliziert werden. Sie müssen eine Zeitpunktwiederherstellung durchführen, um eine Wiederherstellung solcher Fehler zu ermöglichen. |
| <b>Verfügbarkeitszonenfehler | Wenn Sie eine Wiederherstellung nach einem Ausfall auf Zonenebene durchführen möchten, können Sie eine Zeitpunktwiederherstellung mithilfe der Sicherung und der Auswahl eines benutzerdefinierten Wiederherstellungspunkts mit der neuesten Zeit durchführen, um die neuesten Daten wiederherzustellen. Ein neuer flexibler Server wird in einer anderen, unbeeinträchtigten Zone bereitgestellt. Die für die Wiederherstellung benötigte Zeit hängt von der vorherigen Sicherung und dem Volume der wiederherzustellenden Transaktionsprotokolle ab. | Für den flexiblen Server erfolgt automatisch ein Failover auf den Standbyserver innerhalb von 60–120 s ohne Datenverlust. Weitere Informationen finden Sie auf der [Seite „Hochverfügbarkeitskonzepte“](./concepts-high-availability.md). | 
| <b> Regionsausfall | Regionsübergreifende Lesereplikat- und Geowiederherstellung von Sicherungsfunktionen wird während der Vorschauphase noch nicht unterstützt. | |


> [!IMPORTANT]
> Gelöschte Server **können nicht** wiederhergestellt werden. Wenn Sie den Server löschen, werden auch alle Datenbanken gelöscht, die zum Server gehören, und können nicht wiederhergestellt werden. Verwenden Sie die [Azure-Ressourcensperre](../../azure-resource-manager/management/lock-resources.md), um einer versehentlichen Löschung Ihres Servers vorzubeugen.


## <a name="next-steps"></a>Nächste Schritte

-   Weitere Informationen zur [zonenredundanten Hochverfügbarkeit](./concepts-high-availability.md)
-   Weitere Informationen zu [Sicherung und Wiederherstellung](./concepts-backup-restore.md)