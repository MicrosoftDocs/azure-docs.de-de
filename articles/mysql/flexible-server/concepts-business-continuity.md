---
title: 'Übersicht über Geschäftskontinuität: Azure Database for MySQL: Flexible Server'
description: Erfahren Sie mehr über die Konzepte der Geschäftskontinuität mit Azure Database for MySQL Flexible Server
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: c29e952e22aaccf31c10de8f6e16d240b4660a23
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240714"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mysql---flexible-server-preview"></a>Übersicht über die Geschäftskontinuität mit Azure Database for MySQL – Flexible Server (Vorschau)

> [!IMPORTANT]
> Azure Database for MySQL Flexible Server befindet sich aktuell in der öffentlichen Vorschau.

Azure Database for MySQL Flexible Server ermöglicht Funktionen für die Geschäftskontinuität, die Ihre Datenbanken im Falle eines geplanten und ungeplanten Ausfalls schützen. Features wie automatische Sicherungen und Hochverfügbarkeit adressieren verschiedene Ebenen des Fehlerschutzes mit unterschiedlichen Wiederherstellungszeiten und Datenverlusten. Wenn Sie Ihre Anwendung zum Schutz vor Fehlern gestalten, sollten Sie für jede Anwendung das Recovery Time Objective (RTO) und das Recovery Point Objective (RPO) berücksichtigen. RTO ist die Downtimetoleranz und RPO die Datenverlusttoleranz nach einer Unterbrechung des Datenbankdiensts.

Die nachstehende Tabelle veranschaulicht die Features, die Flexible Server bietet.

| **Feature** | **Beschreibung** | **Einschränkungen** |
| ---------- | ----------- | ------------ |
| **Sicherung und Wiederherstellung** | Flexible Server führt automatisch tägliche Sicherungen Ihrer Datenbankdateien durch und sichert kontinuierlich Transaktionsprotokolle. Sicherungen können zwischen 1 und 35 Tagen aufbewahrt werden. Sie können Ihren Datenbankserver zu jedem beliebigen Zeitpunkt innerhalb des Aufbewahrungszeitraums Ihrer Sicherung wiederherstellen. Die Wiederherstellungszeit hängt von der Größe der wiederherzustellenden Daten und der Zeit für die Durchführung der Protokollwiederherstellung ab. Weitere Informationen finden Sie unter [Konzepte: Sicherung und Wiederherstellung](./concepts-backup-restore.md). |Sicherungsdaten verbleiben innerhalb der Region |
| **Lokal redundante Sicherung** | Flexible Server-Sicherungen werden automatisch und sicher in einem lokal redundanten Speicher innerhalb einer Region sowie in derselben Verfügbarkeitszone gespeichert. Die lokal redundanten Sicherungen replizieren die Sicherungsdatendateien des Servers dreimal innerhalb eines einzelnen physischen Standorts in der primären Region. Lokal redundanter Sicherungsspeicher stellt eine Dauerhaftigkeit von mindestens 99,999999999 % (11 Neunen) für Objekte in einem bestimmten Jahr bereit. Weitere Informationen finden Sie unter [Konzepte: Sicherung und Wiederherstellung](./concepts-backup-restore.md).| Anwendbar in allen Regionen |
| **Zonenredundante Hochverfügbarkeit** | Flexible Server kann im Hochverfügbarkeitsmodus bereitgestellt werden, bei dem primäre und Standbyserver in zwei verschiedenen Verfügbarkeitszonen innerhalb einer Region bereitgestellt werden. Dies schützt vor Fehlern auf Zonenebene und hilft auch bei der Reduzierung der Downtime von Anwendungen während geplanter und ungeplanter Downtimeereignisse. Die Daten vom primären Server werden synchron mit dem Standbyreplikat repliziert. Bei allen Downtimeereignissen erfolgt für den Datenbankserver automatisch ein Failover auf das Standbyreplikat. Weitere Informationen finden Sie unter [Konzepte: Hochverfügbarkeit](./concepts-high-availability.md). | Unterstützt auf den Computeebenen „Allgemeiner Zweck“ und „Arbeitsspeicheroptimiert“. Nur verfügbar in Regionen, in denen mehrere Zonen verfügbar sind.|
| **Premium-Dateifreigaben** | Die Datenbankdateien werden in sehr langlebigen und zuverlässigen Azure Premium-Dateifreigaben gespeichert, die Datenredundanz mit drei Replikatkopien bieten, die in einer Verfügbarkeitszone mit automatischer Datenwiederherstellung gespeichert sind. Weitere Informationen finden Sie unter [Premium-Dateifreigaben](../../storage/files/storage-how-to-create-premium-fileshare.md). | In einer Verfügbarkeitszone gespeicherte Daten |

> [!IMPORTANT]
> Während des Vorschauzeitraums werden weder Uptime noch RTO und RPO SLAs angeboten. Die Angaben auf dieser Seite dienen nur zu Ihrer Information und zu Planungszwecken.

## <a name="planned-downtime-mitigation"></a>Minimierung von geplanter Downtime

Hier folgen einige geplante Wartungsszenarien, die zu Downtime führen:

| **Szenario** | **Process**|
| :------------ | :----------- |
| **Computeskalierung (Benutzer)**| Wenn Sie einen Computeskalierungsvorgang durchführen, wird mithilfe der skalierten Computekonfiguration ein neuer flexibler Server bereitgestellt. Auf dem vorhandenen Datenbankserver können aktive Prüfpunkte abgeschlossen werden, Clientverbindungen werden entladen, alle Transaktionen ohne Commit werden abgebrochen, und anschließend wird der Server heruntergefahren. Der Speicher wird dann an den neuen Server angefügt und die Datenbank gestartet, die bei Bedarf eine Wiederherstellung durchführt, bevor sie Clientverbindungen akzeptiert. |
| **Bereitstellung neuer Software (Azure)** | Die Einführung neuer Features oder Behebung von Fehlern erfolgt automatisch als Teil der geplanten Wartungsarbeiten des Diensts, und Sie können planen, wann diese Aktivitäten stattfinden sollen. Weitere Informationen finden Sie in der [Dokumentation](https://aka.ms/servicehealthpm) und im [Portal](https://aka.ms/servicehealthpm). |
| **Upgrades auf Nebenversionen (Azure)** | Azure Database for MySQL patcht Datenbankserver automatisch auf die von Azure festgelegte Nebenversion. Dies erfolgt im Rahmen der geplanten Wartung des Diensts. Dabei kommt es zu einer kurzen Downtime von ein paar Sekunden. Danach wird der Datenbankserver automatisch mit der neuen Nebenversion neu gestartet. Weitere Informationen finden Sie in der [Dokumentation](../concepts-monitoring.md#planned-maintenance-notification) und im [Portal](https://aka.ms/servicehealthpm).|

Wenn der flexible Server mit **zonenredundanter Hochverfügbarkeit** konfiguriert ist, führt der flexible Server Vorgänge zuerst auf dem Standbyserver und dann auf dem primären Server ohne Failover durch. Weitere Informationen finden Sie unter [Konzepte: Hochverfügbarkeit](./concepts-high-availability.md).

## <a name="unplanned-downtime-mitigation"></a>Minimierung von ungeplanter Downtime

Ungeplante Downtimes können aufgrund von unvorhergesehenen Fehlern auftreten, darunter Fehler in der zugrunde liegenden Hardware, Netzwerkprobleme und Softwarefehler. Wenn der Datenbankserver unerwartet ausfällt, wird das Standbyreplikat aktiviert, sofern er mit Hochverfügbarkeit konfiguriert ist. Wenn dies nicht der Fall ist, wird automatisch ein neuer Datenbankserver bereitgestellt. Ungeplante Downtime lässt sich zwar nicht vollständig vermeiden, wird aber vom flexiblen Server durch automatisches Ausführen von Wiederherstellungsvorgängen sowohl auf Ebene der Datenbankserver als auch des Speichers minimiert, ohne dass ein Eingreifen durch einen Benutzer erforderlich ist.

### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Ungeplante Downtime: Fehlerszenarios und Dienstwiederherstellung

Im Folgenden finden Sie einige ungeplante Fehlerszenarien und den Wiederherstellungsprozess:

| **Szenario** | **Wiederherstellungsprozess [ohne Hochverfügbarkeit]** | **Wiederherstellungsprozess [mit Hochverfügbarkeit]** |
| :---------- | ---------- | ------- |
| **Ausfall des Datenbankservers** | Wenn der Datenbankserver aufgrund eines Fehlers an der zugrunde liegenden Hardware ausfällt, werden die aktiven Verbindungen getrennt, und alle Transaktionen, die gerade ausgeführt werden, werden abgebrochen. Azure versucht, den Datenbankserver neu zu starten. Wenn dies erfolgreich ist, wird die Wiederherstellung der Datenbank ausgeführt. Wenn beim Neustart ein Fehler auftritt, wird versucht, den Datenbankserver auf einem anderen physischen Knoten neu zu starten.  <br /> <br /> Die Wiederherstellungszeit (Recovery Time Objective, RTO) hängt von verschiedenen Faktoren ab, z. B. der Aktivität zum Zeitpunkt des Fehlers. Zu diesen Aktivitäten zählen große Transaktionen und die Anzahl der Wiederherstellungsvorgänge, die während des Starts des Datenbankservers ausgeführt wurden. <br /> <br /> Anwendungen, die die MySQL-Datenbanken verwenden, müssen so konzipiert sein, dass sie getrennte Verbindungen und Transaktionsfehler erkennen und entsprechende Wiederholungsversuche durchführen.  Wenn die Anwendung einen erneuten Versuch unternimmt, werden die Verbindungen zu dem neu erstellten Datenbankserver geleitet. | Wenn ein Datenbankserverfehler erkannt wird, wird der Standbydatenbankserver aktiviert, wodurch die Downtime verringert wird. Weitere Informationen finden Sie auf der Seite [Hochverfügbarkeitskonzepte](concepts-high-availability.md). Die RTO wird voraussichtlich 60-120 Sekunden betragen, wobei RPO=0 ist. |
| **Speicherfehler** | Speicherbezogene Probleme wie der Ausfall eines Datenträgers oder physische Blockbeschädigungen haben keine Auswirkungen auf Anwendungen. Da drei Kopien der Daten gespeichert werden, wird eine Kopie der Daten vom verbleibenden Speicher bereitgestellt. Blockbeschädigungen werden automatisch behoben. Wenn eine Kopie der Daten verloren geht, wird automatisch eine neue erstellt. | Bei nicht behebbaren Fehlern erfolgt ein Failover des flexiblen Servers auf das Standbyreplikat, um die Downtime zu reduzieren. Weitere Informationen finden Sie auf der Seite [Hochverfügbarkeitskonzepte](./concepts-high-availability.md). |
| **Logische Fehler/Benutzerfehler** | Die Wiederherstellung bei Benutzerfehlern, z. B. versehentlich gelöschten Tabellen oder falsch aktualisierten Daten, umfasst das Durchführen einer [Zeitpunktwiederherstellung](concepts-backup-restore.md), bei der die Daten bis zu einem Zeitpunkt kurz vor dem Fehler wiederhergestellt werden.<br> <br>  Wenn Sie anstelle aller Datenbanken auf dem Datenbankserver nur eine Teilmenge von Datenbanken oder bestimmte Tabellen wiederherstellen möchten, können Sie den Datenbankserver in einer neuen Instanz wiederherstellen, die Tabelle(n) über [pg_dump](https://www.postgresql.org/docs/current/app-pgdump.html) exportieren und sie dann über [pg_restore](https://www.postgresql.org/docs/current/app-pgrestore.html) in Ihrer Datenbank wiederherstellen. | Diese Benutzerfehler sind nicht durch Hochverfügbarkeit geschützt, da alle Benutzervorgänge auch auf den Standbyserver repliziert werden. |
| **Verfügbarkeitszonenfehler** | Es ist zwar ein seltenes Ereignis, aber wenn Sie eine Wiederherstellung nach einem Ausfall auf Zonenebene durchführen möchten, können Sie mithilfe der Sicherung und der Auswahl eines benutzerdefinierten Wiederherstellungspunkts eine Zeitpunktwiederherstellung durchführen, um auf die neuesten Daten zuzugreifen. Ein neuer flexibler Server wird in einer anderen Zone bereitgestellt. Die für die Wiederherstellung benötigte Zeit hängt von der vorherigen Sicherung und der Anzahl der wiederherzustellenden Transaktionsprotokolle ab. | Der flexible Server führt ein automatisches Failover auf den Standbystandort durch. Weitere Informationen finden Sie auf der Seite [Hochverfügbarkeitskonzepte](./concepts-high-availability.md). |
| **Regionsausfall** | Regionsübergreifende Features zur Replikat- und Geowiederherstellung werden in der Vorschau noch nicht unterstützt. | |

> [!IMPORTANT]
> Gelöschte Server **können nicht** wiederhergestellt werden. Wenn Sie den Server löschen, werden auch alle Datenbanken gelöscht, die zum Server gehören, und können nicht wiederhergestellt werden. Verwenden Sie die [Azure-Ressourcensperre](../../azure-resource-manager/management/lock-resources.md), um einer versehentlichen Löschung Ihres Servers vorzubeugen.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr zur [zonenredundanten Hochverfügbarkeit](./concepts-high-availability.md)
- Weitere Informationen zu [Sicherung und Wiederherstellung](./concepts-backup-restore.md)
