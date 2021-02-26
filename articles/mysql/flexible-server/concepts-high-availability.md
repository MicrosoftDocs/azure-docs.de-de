---
title: Übersicht über zonenredundante Hochverfügbarkeit mit Azure Database for MySQL Flexible Server
description: In diesem Artikel lernen Sie die Konzepte der zonenredundanten Hochverfügbarkeit mit Azure Database for MySQL Flexible Server kennen.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: f01a0869f7786ee6197835610456f4bb1cbd6b03
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097116"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Hochverfügbarkeitskonzepte in Azure Database for MySQL Flexible Server (Vorschau)

> [!IMPORTANT] 
> Azure Database for MySQL – Flexible Server befindet sich aktuell in der öffentlichen Vorschau.

Azure Database for MySQL Flexible Server (Vorschau) ermöglicht Ihnen die Konfiguration der Hochverfügbarkeit mit automatischem Failover mithilfe der Option für **zonenredundante** Hochverfügbarkeit. Wenn Flexible Server in einer zonenredundanten Konfiguration bereitgestellt wird, wird in einer anderen Verfügbarkeitszone automatisch ein Standbyreplikat bereitgestellt und verwaltet. Mithilfe der Speicherebenenreplikation werden die Daten mit dem Standbyserver in der sekundären Zonen **synchron repliziert**, um dafür zu sorgen, dass es bei einem Failover zu keinerlei Datenverlust kommt. Das Failover ist für die Clientanwendung vollständig transparent und benötigt keinerlei Benutzeraktionen. Der Standbyserver ist nicht für Lese- oder Schreibvorgänge verfügbar, fungiert jedoch als passive Reserve, um ein schnelles Failover zu ermöglichen. Die Dauer für Failover befindet sich in der Regel zwischen 60 und 120 Sekunden.

Eine Konfiguration der zonenredundanten Hochverfügbarkeit ermöglicht ein automatisches Failover während geplanten Ereignissen wie von Benutzern ausgehende Vorgänge zum Skalieren von Computekapazitäten sowie ungeplanten Ereignissen wie Fehler der zugrunde liegenden Hardware und Software, Netzwerkausfälle oder sogar Ausfälle von Verfügbarkeitszonen.

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="Ansicht der zonenredundanten Hochverfügbarkeit":::

## <a name="zone-redundancy-architecture"></a>Architektur der Zonenredundanz

Der primäre Server wird in der Region und in einer bestimmten Verfügbarkeitszone bereitgestellt. Wenn die Hochverfügbarkeit ausgewählt wird, wird automatisch ein Standbyreplikatserver mit derselben Konfiguration wie der primäre Server bereitgestellt, einschließlich identischer Computeebene, Computegröße, Speichergröße und Netzwerkkonfiguration. Die Protokolldaten werden synchron mit dem Standbyreplikat repliziert, um dafür zu sorgen, dass es bei Fehlern zu keinerlei Datenverlust kommt. Automatische Sicherungen, sowohl Momentaufnahmen als auch Protokollsicherungen, werden auf dem primären Datenbankserver durchgeführt. 

Die Integrität der Hochverfügbarkeit wird ununterbrochen überwacht und an die Übersichtsseite gemeldet.

Eine Übersicht über die verschiedenen Replikationsstatus finden Sie unten:

| **Status** | **Beschreibung** |
| :----- | :------ |
| <b>NotEnabled | Die zonenredundante Hochverfügbarkeit ist nicht aktiviert. |
| <b>CreatingStandby | Ein neues Standbyreplikat wird erstellt. |
| <b>ReplicatingData | Nach Erstellung des Standbyreplikats wird es mit dem primären Server synchronisiert. |
| <b>FailingOver | Der Datenbankserver führt ein Failover vom primären Server auf das Standbyreplikat durch. |
| <b>Healthy | Die zonenredundante Hochverfügbarkeit weist einen stabilen Zustand auf, und die Integrität ist gesichert. |
| <b>RemovingStandby | Auf Grundlage einer Benutzeraktion wird das Standbyreplikat gelöscht.| 

## <a name="advantages"></a>Vorteile

Unten finden Sie einige Vorteile der Verwendung des Features für zonenredundante Hochverfügbarkeit: 

-   Standbyreplikate werden hinsichtlich Anzahl virtueller Kerne, Speicher und Netzwerkeinstellungen (virtuelles Netzwerk, Firewall) mit der exakt gleichen VM-Konfiguration wie das primäre Replikat bereitgestellt.
-   Es besteht die Möglichkeit, Standbyreplikate zu entfernen, indem die Hochverfügbarkeit deaktiviert wird.
-   Automatische Sicherungen basieren auf Momentaufnahmen, werden auf dem primären Datenbankserver durchgeführt und in einem zonenredundanten Speicher gespeichert.
-   Bei einem Failover führt der flexible Azure Database for MySQL-Server automatisch ein Failover zum Standbyreplikat aus, wenn Hochverfügbarkeit aktiviert ist. Das Hochverfügbarkeitssetup überwacht den primären Server und schaltet ihn wieder online.
-   Clients stellen immer eine Verbindung mit dem primären Datenbankserver her.
-   Wenn ein Datenbankabsturz oder Knotenfehler auftritt, wird der Neustartversuch zuerst auf demselben Knoten durchgeführt. Wenn dies nicht erfolgreich ist, wird das automatische Failover ausgelöst.
-   Es besteht die Möglichkeit, den Server neu zu starten, sodass alle Änderungen an statischen Serverparametern übernommen werden.

## <a name="steady-state-operations"></a>Vorgänge mit stabilem Zustand

Anwendungen sind mit dem primären Server über den Datenbankservernamen verbunden. Die Informationen des Standbyreplikats werden nicht für den Direktzugriff verfügbar gemacht. Commit- und Schreibvorgänge werden für die Anwendung erst bestätigt, nachdem die Protokolldateien sowohl auf dem Datenträger des primären Servers als auch auf dem Standbyreplikat synchron gespeichert sind. Aufgrund dieser zusätzlichen Roundtripanforderung ist bei Anwendungen mit einer höheren Latenz für Schreib- und Commitvorgänge zu rechnen. Sie können die Integrität der Hochverfügbarkeit über das Portal überwachen.

## <a name="failover-process"></a>Failoverprozess 
Zur Gewährleistung einer Geschäftskontinuität müssen Sie über einen Failoverprozess für geplante und ungeplante Ereignisse verfügen. 

### <a name="planned-events"></a>Geplante Ereignisse

Zu geplanten Downtimeereignissen gehören Aktivitäten, die von Azure geplant werden, z. B. regelmäßige Softwareupdates und Upgrades für Nebenversionen, oder Aktivitäten, die von Kunden ausgehen, z. B. Vorgänge zum Skalieren von Compute- oder Speicherkapazitäten. Alle diese Änderungen werden zunächst auf das Standbyreplikat angewendet. Während dieses Zeitraums greifen die Anwendungen weiterhin auf den primären Server zu. Sobald das Standbyreplikat aktualisiert wurde, werden die Verbindungen des primären Servers angehalten und ihre Warteschlangen abgearbeitet. Dabei wird ein Failover ausgelöst, das das Standbyreplikat als primären Server mit identischem Datenbankservernamen aktiviert, indem der DNS-Eintrag aktualisiert wird. Clientverbindungen werden getrennt und müssen eine neue Verbindung herstellen und ihre Vorgänge fortsetzen. In der Zone des ehemaligen primären Servers wird ein neuer Standbyserver erstellt. Die Failovergesamtdauer beträgt erwartungsgemäß zwischen 60 und 120 Sekunden. 

>[!NOTE]
> Im Fall eines Computeskalierungsvorgangs wird der sekundäre Replikatserver gefolgt vom primären Server skaliert. Dabei ist kein Failover beteiligt.

### <a name="failover-process---unplanned-events"></a>Failoverprozess: nicht geplante Ereignisse
Zu nicht geplanten Dienstdowntimes gehören Softwarefehler oder Infrastrukturausfälle wie bei Compute-, Netzwerk- oder Speicherfehlern oder Stromausfälle, die die Verfügbarkeit der Datenbank beeinträchtigen. Falls eine Datenbank nicht verfügbar ist, wird die Replikation auf das Standbyreplikat abgebrochen, und das Standbyreplikat wird als primäre Datenbank aktiviert. Das DNS wird aktualisiert, und Clients stellen dann eine neue Verbindung zum Datenbankserver her und setzen ihre Vorgänge fort. Die Failovergesamtdauer beträgt erwartungsgemäß zwischen 60 und 120 Sekunden. Je nach Aktivität auf dem primären Datenbankserver zum Zeitpunkt des Failovers, z. B. große Transaktionen, und abhängig von der Wiederherstellungszeit kann das Failover jedoch auch etwas länger dauern.

## <a name="schedule-maintenance-window"></a>Zeitplan für Wartungsfenster 

Flexible Server bieten die Möglichkeit, die Wartung zu planen. Dabei können Sie ein dreißigminütiges Zeitfenster an einem Tag Ihrer Wahl auswählen, während dem Wartungsarbeiten in Azure wie Patchimplementierungen oder Nebenversionsupgrades durchgeführt werden. Für Ihre flexiblen Server mit konfigurierter Hochverfügbarkeit werden diese Wartungsaktivitäten zuerst auf dem Standbyreplikat durchgeführt. 

## <a name="point-in-time-restore"></a>Wiederherstellung bis zu einem bestimmten Zeitpunkt 
Da Flexible Server mit konfigurierter Hochverfügbarkeit Daten synchron repliziert, sind Standbyserver und primärer Server immer auf dem gleichen aktuellen Stand. Fehler durch Benutzer auf dem primären Replikat, z. B. das versehentliche Löschen einer Tabelle oder fehlerhafte Aktualisierungen, werden identisch für den Standbyserver übernommen. Folglich können Standbyserver nicht dazu verwendet werden, Wiederherstellungen für solche logischen Fehler durchzuführen. Zur Wiederherstellung solcher logischen Fehler müssen Sie eine Zeitpunktwiederherstellung für einen Zeitpunkt durchführen, der vor dem Auftreten des Fehlers liegt. Mithilfe der Zeitpunktwiederherstellungsfunktion von Flexible Server wird beim Wiederherstellen einer Datenbank mit Hochverfügbarkeit ein neuer Datenbankserver als neuer flexibler Server mit einem vom Benutzer angegebenen Namen wiederhergestellt. Sie können dann das Objekt vom Datenbankserver exportieren und in Ihren Produktionsdatenbankserver importieren. Wenn Sie Ihren Datenbankserver für Test- und Entwicklungszwecke klonen möchten oder wenn Sie ihn aus anderen Gründen wiederherstellen möchten, können Sie in ähnlicher Weise entweder den neuesten Wiederherstellungspunkt oder einen benutzerdefinierten Wiederherstellungspunkt auswählen. Beim Wiederherstellungsvorgang wird ein flexibler Server mit einzelner Zone erstellt.

## <a name="mitigate-downtime"></a>Minimieren von Downtime 
Wenn Sie nicht das Feature für zonenredundante Hochverfügbarkeit nutzen, müssen Sie dennoch in der Lage sein, die Downtime für Ihre Anwendung zu minimieren. Dienstdowntimes, z. B. im Rahmen geplanter Patches, Upgrades für Nebenversionen oder von Benutzern ausgehenden Vorgängen, können innerhalb geplanter Wartungsfensters eingeschlossen werden. Geplante Dienstdowntimes, z. B. geplante Patches, Upgrades für Nebenversionen oder von Benutzern ausgehende Vorgänge wie die Skalierung von Computekapazitäten, sorgen für Downtime. Sie können sich dazu entschließen, von Azure ausgehende Wartungstasks für die Wochentage und die Uhrzeiten zu planen, die sich am wenigsten auf die Anwendung auswirken, um die Beeinträchtigungen möglichst gering zu halten. 

Während ungeplanter Downtimes, beispielsweise als Folge eines Datenbankabsturzes oder einem Serverausfall, wird der beeinträchtigte Server innerhalb derselben Zone neu gestartet. Wenn eine gesamte Zone betroffen sein sollte, was selten vorkommt, können Sie die Datenbank in einer anderen Zone derselben Region wiederherstellen. 

## <a name="things-to-know-with-zone-redundancy"></a>Wichtige Informationen zur Zonenredundanz 

Unten finden Sie einige Hinweise, die Sie beachten sollten, wenn Sie die zonenredundante Hochverfügbarkeit nutzen: 

-   Hochverfügbarkeit kann **ausschließlich** während der Erstellung eines flexiblen Servers festgelegt werden.
-   Hochverfügbarkeit wird für burstfähige Computeebenen nicht unterstützt.
-   Aufgrund der synchronen Replikation in eine andere Verfügbarkeitszone kann es beim primären Datenbankserver zu einer erhöhten Latenz für Schreib- und Commitvorgänge kommen.
-   Standbyreplikate können nicht für schreibgeschützte Abfragen verwendet werden.
-   Je nach Aktivität des primären Servers zum Zeitpunkt eines Failovers kann es zwischen 60 und 120 Sekunden oder länger dauern, bis das Failover abgeschlossen ist.
-   Ein Neustart des primären Datenbankservers für eine Übernahme von Änderungen an statischen Parametern führt auch zu einem Neustart des Standbyreplikats.
-   Die Konfiguration von Lesereplikaten für Server mit zonenredundanter Hochverfügbarkeit wird nicht unterstützt.
-   Das Konfigurieren von Verwaltungsaufgaben, die vom Kunden ausgehen, kann während eines verwalteten Wartungsfensters nicht automatisiert werden.
-   Geplante Ereignisse wie das Skalieren von Computekapazitäten und Upgrades für Nebenversionen werden für den primären Server und das Standbyreplikat zur selben Zeit durchgeführt. 


## <a name="next-steps"></a>Nächste Schritte

-   Weitere Informationen zur [Geschäftskontinuität](./concepts-business-continuity.md)
-   Weitere Informationen zur [zonenredundanten Hochverfügbarkeit](./concepts-high-availability.md)
-   Weitere Informationen zu [Sicherung und Wiederherstellung](./concepts-backup-restore.md)
