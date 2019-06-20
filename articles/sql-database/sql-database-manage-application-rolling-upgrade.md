---
title: Parallele Anwendungsupgrades – Azure SQL-Datenbank | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Georeplikation von Azure SQL-Datenbank verwenden, um Onlineupgrades Ihrer Cloudanwendung zu unterstützen.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 02/13/2019
ms.openlocfilehash: 47fd6c1e2bb342bc1a31fb16a45a5ebc749dca69
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60702615"
---
# <a name="manage-rolling-upgrades-of-cloud-applications-by-using-sql-database-active-geo-replication"></a>Verwalten von parallelen Upgrades von Cloudanwendungen mithilfe der aktiven Georeplikation von SQL-Datenbank

Erfahren Sie, wie Sie die [aktive Georeplikation](sql-database-auto-failover-group.md) in Azure SQL-Datenbank verwenden, um parallele Upgrades Ihrer Cloudanwendung zu aktivieren. Da Upgrades unterbrechende Vorgänge darstellen, sollten sie Teil der Planung und des Designs Ihrer Geschäftskontinuität sein. In diesem Artikel betrachten wir zwei unterschiedliche Methoden zum Orchestrieren des Upgradevorgangs und erörtern die Vor- und Nachteile jeder Option. Für die Zwecke dieses Artikels verwenden wir eine Anwendung, die aus einer mit einer Einzeldatenbank als Datenebene verbundenen Website besteht. Unser Ziel ist, die Version 1 (V1) der Anwendung auf die Version 2 (V2) zu aktualisieren, ohne dass sich dies deutlich auf die Benutzererfahrung auswirkt.

Berücksichtigen Sie beim Auswerten der Upgradeoptionen folgende Faktoren:

* Auswirkungen auf die Verfügbarkeit der Anwendung während Upgrades, z.B. wie lange Anwendungsfunktionen eingeschränkt oder beeinträchtigt werden könnten.
* Möglichkeit zum Rollback, falls beim Upgrade ein Fehler auftritt.
* Das Sicherheitsrisiko für die Anwendung, wenn während des Upgrades ein schwerwiegender Fehler auftritt, der nicht in Zusammenhang mit dem Upgrade steht.
* Die insgesamt anfallenden Kosten. Dieser Faktor schließt zusätzliche Datenbankredundanz und Mehrkosten der vom Upgradevorgang verwendeten temporären Komponenten ein.

## <a name="upgrade-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Upgraden von Anwendungen, die sich auf Datenbanksicherungen für die Notfallwiederherstellung verlassen

Wenn sich Ihre Anwendung auf automatische Datenbanksicherungen verlässt und die Geowiederherstellung zur Notfallwiederherstellung verwendet, wird sie in einer einzelnen Azure-Region bereitgestellt. Um die Unterbrechung für den Benutzer zu minimieren, erstellen Sie in dieser Region eine Stagingumgebung mit allen am Upgrade beteiligten Anwendungskomponenten. Das erste Diagramm veranschaulicht die Betriebsumgebung vor dem Upgradevorgang. Der Endpunkt `contoso.azurewebsites.net` stellt eine Produktionsumgebung der Webanwendung dar. Sie müssen eine Stagingumgebung mit einer vollständig synchronisierten Kopie der Datenbank erstellen, um ein Rollback des Upgrades zu ermöglichen. Mit diesen Schritten erstellen Sie eine Stagingumgebung für das Upgrade:

1. Erstellen Sie eine sekundäre Datenbank in derselben Azure-Region. Überwachen Sie die sekundäre Datenbank, um festzustellen, ob der Seedingprozess abgeschlossen ist (1).
2. Erstellen Sie eine neue Umgebung für Ihre Web-App, und nennen Sie sie „Staging“. Sie wird in Azure DNS mit der URL `contoso-staging.azurewebsites.net` registriert (2).

> [!NOTE]
> Diese Vorbereitungsschritte wirken sich nicht auf die Produktionsumgebung aus, die im Vollzugriffsmodus funktionieren kann.

![Die SQL-Datenbank-Georeplikationskonfiguration für die cloudbasierte Notfallwiederherstellung.](media/sql-database-manage-application-rolling-upgrade/option1-1.png)

Wenn die Vorbereitungsschritte abgeschlossen sind, ist die Anwendung bereit für das tatsächliche Upgrade. Das folgende Diagramm veranschaulicht die Schritte, die der Upgradevorgang umfasst:

1. Setzen Sie die primäre Datenbank in den schreibgeschützten Modus (3). Durch diesen Modus wird sichergestellt, dass die Produktionsumgebung der Web-App (V1) während des Upgrades schreibgeschützt bleibt, sodass Datenabweichungen zwischen V1- und V2-Datenbankinstanzen vermieden werden.
2. Trennen Sie die sekundäre Datenbank mithilfe des Modus für die geplante Beendigung (4). Diese Aktion erstellt eine vollständig synchronisierte, unabhängige Kopie der primären Datenbank. Diese Datenbank wird upgegradet.
3. Ändern Sie den Modus der sekundären Datenbank in den Lese-/Schreibmodus, und führen Sie das Upgradeskript aus (5).

![Die SQL-Datenbank-Georeplikationskonfiguration für die cloudbasierte Notfallwiederherstellung.](media/sql-database-manage-application-rolling-upgrade/option1-2.png)

Wenn das Upgrade erfolgreich abgeschlossen wurde, können Sie jetzt den Wechsel der Benutzer zur aktualisierten Kopie der Anwendung durchführen, die zur Produktionsumgebung wird. Der Wechsel umfasst ein paar weitere Schritte, wie im folgenden Diagramm dargestellt:

1. Aktivieren Sie einen Swapvorgang zwischen Produktions- und Stagingumgebungen der Web-App (6). Dieser Vorgang wechselt die URLs der beiden Umgebungen. Jetzt verweist `contoso.azurewebsites.net` auf die V2-Version der Website und der Datenbank (Produktionsumgebung). 
2. Wenn Sie die V1-Version, die nach dem Austausch eine Stagingkopie wird, nicht mehr benötigen, können Sie die Stagingumgebung (7) außer Betrieb setzen.

![Die SQL-Datenbank-Georeplikationskonfiguration für die cloudbasierte Notfallwiederherstellung.](media/sql-database-manage-application-rolling-upgrade/option1-3.png)

Wenn der Upgradevorgang nicht erfolgreich ist (z.B. aufgrund eines Fehlers im Upgradeskript), betrachten Sie die Stagingumgebung als kompromittiert. Legen Sie für die Anwendung in der Produktionsumgebung wieder „Vollzugriff“ fest, um sie auf den Status vor dem Upgrade zurückzusetzen. Das nächste Diagramm zeigt die Zurücksetzungsschritte:

1. Legen Sie für die Datenbankkopie den Lese-/Schreibmodus fest (8). Diese Aktion stellt die vollständige V1-Funktionalität in der Produktionskopie wieder her.
2. Führen Sie die Analyse der Grundursache durch, und setzen Sie die Stagingumgebung (9) außer Betrieb.

An dieser Stelle ist die Anwendung voll funktionsfähig, und Sie können die Upgradeschritte wiederholen.

> [!NOTE]
> Das Rollback erfordert keine DNS-Änderungen, da Sie den Swap-Vorgang noch nicht ausgeführt haben.

![Die SQL-Datenbank-Georeplikationskonfiguration für die cloudbasierte Notfallwiederherstellung.](media/sql-database-manage-application-rolling-upgrade/option1-4.png)

Der wichtigste Vorteil dieser Option ist, dass Sie eine Anwendung in einer einzelnen Region mithilfe einer Reihe von einfachen Schritten upgraden können. Die anfallenden Kosten für das Upgrade sind relativ gering. 

Der wichtigste Nachteil ist, dass im Fall eines schwerwiegenden Fehlers während des Upgrades die Wiederherstellung auf den Status vor dem Upgrade beinhaltet, dass die Anwendung in einer anderen Region erneut bereitgestellt und die Datenbank mithilfe der Geowiederherstellung aus einer Sicherung wiederhergestellt wird. Dieser Prozess führt zu erheblicher Downtime.

## <a name="upgrade-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Upgrade von Anwendungen, die für die Notfallwiederherstellung von der Datenbank-Georeplikation abhängen

Wenn Ihre Anwendung aktive Georeplikation oder Autofailover-Gruppen für die Geschäftskontinuität verwendet, wird sie in mindestens zwei verschiedenen Regionen bereitgestellt. Es gibt eine aktive, primäre Datenbank in einer primären Region und eine schreibgeschützte sekundäre Datenbank in einer Sicherungsregion. Zusammen mit den am Anfang dieses Artikels erwähnten Faktoren muss der Upgradevorgang auch Folgendes garantieren:

* Die Anwendung bleibt jederzeit während des Upgrades vor schwerwiegenden Fehlern geschützt.
* Die georedundanten Komponenten der Anwendung werden parallel mit den aktiven Komponenten upgegradet.

Zum Erreichen dieser Ziele nutzen Sie neben den Umgebungen für die Web-Apps den Azure Traffic Manager, indem Sie das Failoverprofil mit einem aktiven Endpunkt und einem Sicherungsendpunkt verwenden. Das folgende Diagramm veranschaulicht die Betriebsumgebung vor dem Upgradevorgang. Die Websites `contoso-1.azurewebsites.net` und `contoso-dr.azurewebsites.net` stellen eine Produktionsumgebung der Anwendung mit vollständiger geografischer Redundanz dar. Die Produktionsumgebung umfasst die folgenden Komponenten:

* Die Produktionsumgebung der Web-App `contoso-1.azurewebsites.net` in der primären Region (1)
* Die primäre Datenbank in der primären Region (2)
* Eine Standbyinstanz der Web-App in der Sicherungsregion (3)
* Die georeplizierte sekundäre Datenbank in der Sicherungsregion (4)
* Ein Traffic Manager-Leistungsprofil mit Onlineendpunkt `contoso-1.azurewebsites.net` und Offlineendpunkt `contoso-dr.azurewebsites.net`

Sie müssen eine Stagingumgebung mit einer vollständig synchronisierten Kopie der Anwendung erstellen, um ein Rollback des Upgrades zu ermöglichen. Die Stagingumgebung muss ebenfalls georedundant sein, da Sie sicherstellen müssen, dass die Anwendung im Fall eines schwerwiegenden Fehlers während des Upgradevorgangs schnell wiederhergestellt werden kann. Anhand der folgenden Schritte erstellen Sie einen Stagingumgebung für das Upgrade:

1. Stellen Sie eine Produktionsumgebung der Web-App in der primären Region bereit (6).
2. Erstellen Sie eine sekundäre Datenbank in der primären Azure-Region (7). Konfigurieren Sie die Stagingumgebung der Web-App, um eine Verbindung damit herzustellen. 
3. Erstellen Sie eine weitere georedundante sekundäre Datenbank in der Sicherungsregion, indem Sie die sekundäre Datenbank in der primären Region replizieren. (Diese Methode wird als *verkettete Georeplikation* bezeichnet.) (8).
4. Stellen Sie eine Stagingumgebung der Web-App-Instanz in der Sicherungsregion (9) bereit, und konfigurieren Sie sie so, dass sie eine Verbindung mit der in Schritt (8) erstellten georedundaten sekundären Datenbank herstellt.

> [!NOTE]
> Diese Vorbereitungsschritte wirken sich nicht auf die Anwendung in der Produktionsumgebung aus. Sie bleibt voll funktionsfähig im Lese-/Schreibmodus.

![Die SQL-Datenbank-Georeplikationskonfiguration für die cloudbasierte Notfallwiederherstellung.](media/sql-database-manage-application-rolling-upgrade/option2-1.png)

Wenn die Vorbereitungsschritte abgeschlossen sind, ist die Stagingumgebung bereit für das Upgrade. Das folgende Diagramm veranschaulicht diese Upgradeschritte:

1. Legen Sie für die primäre Datenbank in der Produktionsumgebung den schreibgeschützten Modus fest (10). Dieser Modus stellt sicher, dass die Produktionsdatenbank (V1) während des Upgrades unverändert bleibt, sodass Datenabweichungen zwischen V1- und V2-Datenbankinstanzen vermieden werden.

```sql
-- Set the production database to read-only mode
ALTER DATABASE <Prod_DB>
SET (ALLOW_CONNECTIONS = NO)
```

2. Trennen die sekundäre Datenbank (11), um die Georeplikation zu beenden. Diese Aktion erstellt eine unabhängige, aber vollständig synchronisierte Kopie der Produktionsdatenbank. Diese Datenbank wird upgegradet. Im folgenden Beispiel wird Transact-SQL verwendet, [PowerShell](/powershell/module/az.sql/remove-azsqldatabasesecondary?view=azps-1.5.0) ist jedoch auch verfügbar. 

```sql
-- Disconnect the secondary, terminating geo-replication
ALTER DATABASE <Prod_DB>
REMOVE SECONDARY ON SERVER <Partner-Server>
```

3. Führen Sie das Upgradeskript für `contoso-1-staging.azurewebsites.net`, `contoso-dr-staging.azurewebsites.net` und die primäre Stagingdatenbank aus (12). Die Änderungen in der Datenbank werden automatisch in die sekundäre Stagingdatenbank repliziert.

![Die SQL-Datenbank-Georeplikationskonfiguration für die cloudbasierte Notfallwiederherstellung.](media/sql-database-manage-application-rolling-upgrade/option2-2.png)

Wenn das Upgrade erfolgreich abgeschlossen ist, können die Benutzer jetzt zu der V2-Version der Anwendung wechseln. Das folgende Diagramm veranschaulicht die dafür notwendigen Schritte:

1. Aktivieren Sie einen Swapvorgang zwischen Produktions- und Stagingumgebung der Web-App in der primären Region (13) und in der Sicherungsregion (14). V2 der Anwendung wird jetzt eine Produktionsumgebung mit einer redundanten Kopie in der Sicherungsregion.
2. Wenn Sie die V1-Anwendung (15 und 16) nicht mehr benötigen, können Sie die Stagingumgebung außer Betrieb setzen.

![Die SQL-Datenbank-Georeplikationskonfiguration für die cloudbasierte Notfallwiederherstellung.](media/sql-database-manage-application-rolling-upgrade/option2-3.png)

Wenn der Upgradevorgang nicht erfolgreich ist (z.B. aufgrund eines Fehlers im Upgradeskript), sollten Sie die Stagingumgebung als inkonsistent betrachten. Um die Anwendung wieder auf den Status vor dem Upgrade zurückzusetzen, verwenden Sie wieder V1 der Anwendung in der Produktionsumgebung. Die Schritte hierzu werden im nächsten Diagramm angezeigt:

1. Legen Sie für die Kopie der primären Datenbank in der Produktionsumgebung den Lese-/Schreibmodus fest (17). Diese Aktion stellt die vollständige V1-Funktionalität in der Produktionsumgebung wieder her.
2. Führen Sie die Analyse der Grundursache durch, und reparieren oder entfernen Sie die Stagingumgebung (18 und 19).

An dieser Stelle ist die Anwendung voll funktionsfähig, und Sie können die Upgradeschritte wiederholen.

> [!NOTE]
> Das Rollback erfordert keine DNS-Änderungen, da Sie den Swapvorgang nicht ausgeführt haben.

![Die SQL-Datenbank-Georeplikationskonfiguration für die cloudbasierte Notfallwiederherstellung.](media/sql-database-manage-application-rolling-upgrade/option2-4.png)

Der wichtigste Vorteil dieser Option ist, dass Sie die Anwendung und ihre georedundante Kopie parallel upgraden können, ohne Ihre Geschäftskontinuität während des Upgrades zu beeinträchtigen.

Der wichtigste Nachteil ist, dass die Option von jeder Anwendungskomponente die doppelte Redundanz erfordert und daher höhere Kosten anfallen. Sie umfasst auch einen komplexeren Workflow.

## <a name="summary"></a>Zusammenfassung

Die beiden in diesem Artikel beschriebenen Upgrademethoden unterscheiden sich hinsichtlich der Komplexität und der anfallenden Kosten. Beide legen jedoch den Fokus auf die Minimierung der Zeit, in der die Benutzer auf schreibgeschützte Vorgänge eingeschränkt sind. Diese Zeit wird direkt durch die Dauer des Upgradeskripts definiert. Sie hängt nicht von der Datenbankgröße, der gewählten Dienstebene, der Websitekonfiguration oder anderen Faktoren ab, die Sie nicht einfach steuern können. Alle Vorbereitungsschritte werden von den Upgradeschritten entkoppelt und haben keine Auswirkungen auf die Produktionsanwendung. Die Effizienz des Upgradeskripts ist der wichtigste Faktor, der die Benutzerfreundlichkeit während Upgrades bestimmt. Sie lässt sich also am besten verbessern, wenn Sie sich darauf konzentrieren, das Upgradeskript so effizient wie möglich zu gestalten.

## <a name="next-steps"></a>Nächste Schritte

* Eine Übersicht und verschiedene Szenarien zum Thema Geschäftskontinuität finden Sie unter [Übersicht über die Geschäftskontinuität](sql-database-business-continuity.md).
* Weitere Informationen zur aktiven Georeplikation von Azure SQL-Datenbank finden Sie unter [Erstellen lesbarer sekundärer Datenbanken mithilfe aktiver Georeplikation](sql-database-active-geo-replication.md).
* Weitere Informationen zu Autofailover-Gruppen für Azure SQL-Datenbank finden Sie unter [Verwenden von Autofailover-Gruppen für ein transparentes und koordiniertes Failover mehrerer Datenbanken](sql-database-auto-failover-group.md).
* Weitere Informationen zu Stagingumgebungen in Azure App Service finden Sie unter [Einrichten von Stagingumgebungen in Azure App Service](../app-service/deploy-staging-slots.md).
* Weitere Informationen zu Azure Traffic Manager-Profilen finden Sie unter [Verwalten von Azure Traffic Manager-Profilen](../traffic-manager/traffic-manager-manage-profiles.md).
