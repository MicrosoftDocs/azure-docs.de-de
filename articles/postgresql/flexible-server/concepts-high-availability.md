---
title: Übersicht über zonenredundante Hochverfügbarkeit für Azure Database for PostgreSQL – Flexible Server (Vorschau)
description: In diesem Artikel lernen Sie die Konzepte zonenredundanter Hochverfügbarkeit für Azure Database for PostgreSQL – Flexible Server kennen.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: c0d9b6042ae695caa73d926653f237b756bf4971
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366722"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql---flexible-server"></a>Hochverfügbarkeitskonzepte für Azure Database for PostgreSQL – Flexible Server

> [!IMPORTANT]
> Azure Database for PostgreSQL – Flexible Server befindet sich in der Vorschau.

Azure Database for PostgreSQL – Flexible Server bietet eine Konfiguration mit Hochverfügbarkeit und automatischer Failoverfunktion mithilfe einer **zonenredundanten** Serverbereitstellung. Wenn Flexible Server in einer zonenredundanten Konfiguration bereitgestellt wird, wird in einer anderen Verfügbarkeitszone automatisch ein Standbyreplikat bereitgestellt und verwaltet. Mithilfe der Streamingreplikation von PostgreSQL werden die Daten im **synchronen** Modus auf den Standbyreplikatserver repliziert. 

Eine zonenredundante Konfiguration ermöglicht ein automatisches Failover ohne Datenverlust während geplanter Ereignisse wie von Benutzern ausgehende Vorgänge zum Skalieren von Computekapazitäten sowie ungeplanter Ereignisse wie Fehler bei der zugrunde liegenden Hardware und Software, Netzwerkausfälle und Ausfälle von Verfügbarkeitszonen. 

:::image type="content" source="./media/business-continuity/concepts-zone-redundant-high-availability-architecture.png" alt-text="Zonenredundante Hochverfügbarkeit"::: 

## <a name="zone-redundant-high-availability-architecture"></a>Zonenredundante Hochverfügbarkeitsarchitektur

Sie können die Region und Verfügbarkeitszone auswählen, um den primären Datenbankserver bereitzustellen. Ein Standbyreplikatserver wird in einer anderen Verfügbarkeitszone mit einer mit dem primären Server identischen Konfiguration bereitgestellt, einschließlich Computeebene, Computegröße, Speichergröße und Netzwerkkonfiguration. Transaktionsprotokolle werden mithilfe der PostgreSQL-Streamingreplikation im synchronen Modus zum Standbyreplikat repliziert. Der primäre Datenbankserver führt regelmäßig automatische Sicherungen durch, während die Transaktionsprotokolle vom Standbyreplikat kontinuierlich im Sicherungsspeicher archiviert werden. 

Die Integrität der Konfiguration für Hochverfügbarkeit wird laufend überwacht und im Portal gemeldet. Die Status zonenredundanter Hochverfügbarkeit sind wie folgt:

| **Status** | **Beschreibung** |
| ------- | ------ |
| <b> Wird initialisiert | Ein neuer Standbyserver wird gerade erstellt. |
| <b> Daten werden repliziert | Nach Erstellung des Standbyservers wird er mit dem primären Server synchronisiert. |
| <b> Fehlerfrei | Die Replikation ist stabil und fehlerfrei. |
| <b> Failover wird ausgeführt | Der Datenbankserver führt ein Failover auf den Standbyserver durch. |
| <b> Standby wird entfernt | Der Standbyserver wird gerade gelöscht. | 
| <b> Nicht aktiviert | Zonenredundante Hochverfügbarkeit ist nicht aktiviert.  |

## <a name="steady-state-operations"></a>Vorgänge mit stabilem Zustand

PostgreSQL-Clientanwendungen sind mit dem primären Server über den Namen des Datenbankservers verbunden. Lesevorgänge der Anwendung werden direkt vom primären Server ermöglicht, während Commit- und Schreibvorgänge in der Anwendung erst dann bestätigt werden, nachdem die Daten sowohl auf dem primären Server als auch auf dem Standbyreplikatserver persistent gespeichert wurden. Aufgrund dieser zusätzlichen Roundtripanforderung ist bei Anwendungen mit einer höheren Latenz für Schreib- und Commitvorgänge zu rechnen. Sie können die Integrität der Hochverfügbarkeit im Portal überwachen.

:::image type="content" source="./media/business-continuity/concepts-high-availability-steady-state.png" alt-text="Zonenredundante Hochverfügbarkeit: stabiler Zustand"::: 

1. Clients stellen eine Verbindung mit der Flexible Server-Instanz her und führen Schreibvorgänge aus.
2. Änderungen werden an den Standbystandort repliziert.
3. Der primäre Server empfängt eine Bestätigung.
4. Schreib-/Commitvorgänge werden bestätigt.

## <a name="failover-process---planned-downtimes"></a>Failoverprozess: geplante Ausfallzeiten

Zu den geplanten Ausfallzeiten zählen regelmäßige geplante Softwareupdates und Upgrades von Nebenversionen in Azure. Bei konfigurierter Hochverfügbarkeit erfolgen diese Vorgänge zunächst für das Standbyreplikat, während die Anwendungen weiterhin auf den primären Server zugreifen. Nachdem das Standbyreplikat aktualisiert wurde, werden die Verbindungen des primären Servers ausgeglichen. Dabei wird ein Failover ausgelöst, durch das das Standbyreplikat als primärer Server mit identischem Datenbankservernamen aktiviert wird. Clientanwendungen müssen sich mit dem gleichen Namen des Datenbankservers wieder mit dem neuen primären Server verbinden und können ihren Betrieb fortsetzen. In der Zone des ehemaligen primären Servers wird ein neuer Standbyserver erstellt. 

Bei anderen vom Benutzer eingeleiteten Vorgängen, wie z. B. Skalieren der Compute- oder Speicherkapazität, erfolgen die Änderungen zuerst auf dem Standby- und dann auf dem primären Server. Derzeit findet für die Verbindungen kein Failover auf den Standbyserver statt, sodass es zu Ausfallzeiten kommt, während der Vorgang auf dem Primärserver erfolgt.

### <a name="reducing-planned-downtime-with-managed-maintenance-window"></a>Verkürzen geplanter Ausfallzeiten mit verwaltetem Wartungsfenster

 Sie können von Azure eingeleitete Wartungsaktivitäten planen, indem Sie ein 30-minütiges Fenster für einen gewünschten Tag wählen, an dem die Aktivitäten in den Datenbanken voraussichtlich gering sein werden. Azure-Wartungsaufgaben wie Patchen oder Upgrades von Nebenversionen erfolgen dann in diesem Zeitfenster.  Für Ihre Flexible Server-Instanzen mit konfigurierter Hochverfügbarkeit werden diese Wartungsaktivitäten zuerst auf dem Standbyreplikat durchgeführt, das anschließend aktiviert wird. Anwendungen stellen dann wieder eine Verbindung mit dem neuen primären Server her und nehmen ihren Betrieb wieder auf, während ein neuer Standbyserver bereitgestellt wird.

## <a name="failover-process---unplanned-downtimes"></a>Failoverprozess: ungeplante Ausfallzeiten

Zu den ungeplanten Ausfällen gehören Softwarefehler oder Ausfälle von Infrastrukturkomponenten, die die Verfügbarkeit der Datenbank beeinträchtigen. Falls das Überwachungssystem erkennt, dass der Server nicht verfügbar ist, wird die Replikation in das Standbyreplikat unterbrochen und das Standbyreplikat als primärer Datenbankserver aktiviert. Clients können sich mit der gleichen Verbindungszeichenfolge wieder mit dem Datenbankserver verbinden und ihren Betrieb fortsetzen. Das gesamte Failover dauert erwartungsgemäß 60 bis 120 Sekunden. Abhängig von der Aktivität auf dem primären Datenbankserver zum Zeitpunkt des Failovers, wie z. B. umfangreiche Transaktionen, und der Wiederherstellungszeit kann das Failover jedoch länger dauern.

:::image type="content" source="./media/business-continuity/concepts-high-availability-failover-state.png" alt-text="Zonenredundante Hochverfügbarkeit: Failover"::: 

1. Der primäre Datenbankserver ist ausgefallen, die Clients verlieren Konnektivität mit der Datenbank. 
2. Der Standbyserver wird als neuer primärer Server aktiviert. Der Client stellt mithilfe derselben Verbindungszeichenfolge eine Verbindung mit dem neuen primären Server her. Wenn sich die Clientanwendung in derselben Zone wie der primäre Datenbankserver befindet, werden Wartezeiten verkürzt und die Leistung verbessert.
3. Der Standbyserver wird in der gleichen Zone wie der alte primäre Server eingerichtet, und die Streamingreplikation wird gestartet. 
4. Sobald die Replikation in stabilem Zustand eingerichtet ist, werden die Commit- und Schreibvorgänge der Clientanwendung bestätigt, nachdem die Daten an beiden Standorten persistent gespeichert wurden.

## <a name="point-in-time-restore"></a>Zeitpunktwiederherstellung 

Mit Hochverfügbarkeit konfigurierte Flexible Server-Instanzen replizieren Daten in Echtzeit auf den Standbyserver, um diesen auf dem neuesten Stand zu halten. Fehler durch Benutzer auf dem primären Server, z. B. versehentliches Löschen einer Tabelle oder fehlerhafte Datenaktualisierungen, werden vom Standbyreplikat identisch übernommen. Daher können Sie den Standbyserver nicht zur Behebung solcher logischen Fehler einsetzen. Um diese Fehler zu beheben, müssen Sie eine Zeitpunktwiederherstellung aus Sicherungen durchführen.  Dank der Möglichkeit der Zeitpunktwiederherstellung für die Flexible Server-Instanz können Sie eine Wiederherstellung zu einem Zeitpunkt vor dem Fehler durchführen. Bei mit Hochverfügbarkeit konfigurierten Datenbanken wird ein neuer Datenbankserver als Flexible Server-Instanz mit einer einzelnen Zone und einem vom Benutzer angegebenen Namen wiederhergestellt. Sie können dann das Objekt vom Datenbankserver exportieren und in Ihren Produktionsdatenbankserver importieren. Wenn Sie Ihren Datenbankserver für Test- und Entwicklungszwecke klonen oder für andere Zwecke wiederherstellen möchten, können Sie auch Zeitpunktwiederherstellungen durchführen.

## <a name="zone-redundant-high-availability---features"></a>Zonenredundante Hochverfügbarkeit: Features

-   Standbyreplikate werden mit einer dem primären Server exakt entsprechenden VM-Konfiguration bereitgestellt, einschließlich virtueller Kerne, Speicher, Netzwerkeinstellungen (VNET, Firewall) usw.

-   Möglichkeit zum Hinzufügen von Hochverfügbarkeit für einen vorhandenen Datenbankserver.

-   Möglichkeit zum Entfernen des Standbyreplikats durch Deaktivieren von Hochverfügbarkeit.

-   Möglichkeit der Wahl Ihrer Verfügbarkeitszone für Ihren primären Datenbankserver.

-   Möglichkeit zum Anhalten, Starten und Neustarten des primären und Standby-Datenbankservers.

-   Automatische Sicherungen werden auf dem primären Datenbankserver durchgeführt und in einem zonenredundanten Speicher gespeichert.

-   Clients stellen immer eine Verbindung mit dem primären Datenbankserver her.

-   Möglichkeit, den Server neu zu starten, sodass alle Änderungen an statischen Serverparametern übernommen werden.
  
-   Regelmäßige Wartungsaktivitäten wie Upgrades von Nebenversionen erfolgen zuerst auf dem Standbyserver, wobei für den Dienst ein Failover stattfindet, um Ausfallzeiten zu verkürzen.  

## <a name="zone-redundant-high-availability---limitations"></a>Zonenredundante Hochverfügbarkeit: Einschränkungen

-   Hochverfügbarkeit wird im Computetarif „Burstfähig“ nicht unterstützt.
-   Hochverfügbarkeit wird nur in Regionen mit mehreren Zonen unterstützt.
-   Aufgrund der synchronen Replikation in eine andere Verfügbarkeitszone kann es bei Anwendungen zu mehr Latenz bei Schreib- und Commitvorgängen kommen.

-   Standbyreplikate können nicht für Leseabfragen verwendet werden.

-   Abhängig von der Workload und der Aktivität auf dem primären Server kann der Failovervorgang länger als 120 Sekunden dauern.

-   Ein Neustart des primären Datenbankservers führt auch zu einem Neustart des Standbyreplikats. 

-   Das Konfigurieren zusätzlicher Lesereplikate wird nicht unterstützt.

-   Das Konfigurieren vom Kunden ausgelöster Verwaltungsaufgaben kann im verwalteten Wartungsfenster nicht geplant werden.

-   Geplante Ereignisse wie das Skalieren von Compute- und Speicherkapazität finden zuerst auf dem Standby- und dann auf dem primären Server statt. Der Server führt für diese geplanten Vorgänge kein Failover aus. 

-  Wenn die logische Decodierung oder logische Replikation mit einem flexiblen Server mit Hochverfügbarkeit konfiguriert ist, werden die logischen Replikationsslots bei einem Failover auf den Standbyserver nicht auf den Standbyserver kopiert.  

## <a name="next-steps"></a>Nächste Schritte

-   Weitere Informationen zur [Geschäftskontinuität](./concepts-business-continuity.md)
-   Weitere Informationen zum [Verwalten von Hochverfügbarkeit](./how-to-manage-high-availability-portal.md)
-   Weitere Informationen zu [Sicherung und Wiederherstellung](./concepts-backup-restore.md)