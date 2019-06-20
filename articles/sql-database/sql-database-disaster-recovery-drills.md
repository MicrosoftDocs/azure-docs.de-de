---
title: Notfallwiederherstellungsverfahren für SQL-Datenbank | Microsoft Docs
description: Hier erhalten Sie Anleitungen und Best Practices dazu, wie Sie Azure SQL-Datenbank verwenden, um Übungen zur Notfallwiederherstellung durchzuführen.
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
ms.date: 12/18/2018
ms.openlocfilehash: 5d754ae558d485036a9a55f573a3f40162ed9f84
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60725430"
---
# <a name="performing-disaster-recovery-drill"></a>Ausführen von Notfallwiederherstellungsverfahren

Es wird empfohlen, in regelmäßigen Abständen Überprüfungen der Anwendungsbereitschaft für den Wiederherstellungsworkflow durchzuführen. Überprüfungen des Anwendungsverhaltens und der Auswirkungen von Datenverlusten und/oder Unterbrechungen durch Failover gehören zu einem angemessenen Entwicklungsverfahren. Solche Überprüfungen werden auch für die meisten Branchenstandards im Zuge von Zertifizierungen zur Geschäftskontinuität vorausgesetzt.

Notfallwiederherstellungsverfahren umfassen Folgendes:

* Simulieren von Ausfällen auf Datenebene
* Wiederherstellen
* Überprüfen der Anwendungsintegrität nach der Wiederherstellung

Abhängig vom [Entwurf Ihrer Anwendung für Geschäftskontinuität](sql-database-business-continuity.md)kann der verbundene Workflow variieren. In diesem Artikel sind die bewährten Methoden zum Durchführen eines Notfallwiederherstellungsverfahrens im Kontext von Azure SQL-Datenbank beschrieben.

## <a name="geo-restore"></a>Geografische Wiederherstellung

Um Datenverluste beim Durchführen eines Notfallwiederherstellungsverfahrens zu verhindern, sollten Sie das Verfahren mit einer Testumgebung ausführen, indem Sie eine Kopie der Produktionsumgebung erstellen und diese zum Überprüfen des Failoverworkflows der Anwendung verwenden.

### <a name="outage-simulation"></a>Simulieren von Ausfällen

Um die Ausfallzeit zu simulieren, können Sie die Quelldatenbank umbenennen. Durch diese Namensänderung werden Verbindungsfehler für die Anwendung verursacht.

### <a name="recovery"></a>Wiederherstellen

* Führen Sie die Geowiederherstellung der Datenbank auf einem anderen Server aus, wie [hier](sql-database-disaster-recovery.md)beschrieben.
* Ändern Sie die Anwendungskonfiguration zum Herstellen einer Verbindung mit der wiederhergestellten Datenbank, und folgen Sie der Anleitung unter [Konfigurieren einer Datenbank nach der Wiederherstellung](sql-database-disaster-recovery.md) zum Abschließen der Wiederherstellung.

### <a name="validation"></a>Überprüfen

Schließen Sie das Verfahren ab, indem Sie die Anwendungsintegrität nach der Wiederherstellung sicherstellen (einschließlich der Verbindungszeichenfolgen, Anmeldungen, grundlegenden Funktionstests oder sonstigen Überprüfungen, die Teil der Standardabmeldeverfahren von Anwendungen sind).

## <a name="failover-groups"></a>Failovergruppen

Für eine durch Failovergruppen geschützte Datenbank beinhaltet das Verfahren ein geplantes Failover auf den sekundären Server. Durch das geplante Failover wird sichergestellt, dass die primäre und die sekundäre Datenbank in der Failovergruppe beim Wechseln der Rollen synchronisiert bleiben. Im Gegensatz zum ungeplanten Failover führt dieser Vorgang nicht zu Datenverlust, daher kann er auch in einer Produktionsumgebung ausgeführt werden.

### <a name="outage-simulation"></a>Simulieren von Ausfällen

Um die Ausfallzeit zu simulieren, können Sie die Web-Anwendung oder den virtuellen Computer deaktivieren, die bzw. der mit der Datenbank verbunden ist. Dieses Simulieren von Ausfällen führt zu Verbindungsfehlern für die Webclients.

### <a name="recovery"></a>Wiederherstellen

* Stellen Sie sicher, dass die Anwendungskonfiguration in der Notfallwiederherstellungsregion auf die vorherige sekundäre Datenbank verweist. Diese Datenbank wird zur neuen primären Datenbank, auf die vollständig zugegriffen werden kann.
* Löst ein [geplantes Failover](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) der Failovergruppe vom sekundären Server aus.
* Führen Sie die Anweisungen unter [Konfigurieren einer Datenbank nach der Wiederherstellung](sql-database-disaster-recovery.md) aus, um die Wiederherstellung abzuschließen.

### <a name="validation"></a>Überprüfen

Schließen Sie das Verfahren ab, indem Sie die Anwendungsintegrität nach der Wiederherstellung überprüfen (einschließlich Funktionalitätstests oder sonstiger Überprüfungen, die für die Verfahrensgenehmigungen erforderlich sind).

## <a name="next-steps"></a>Nächste Schritte

* Informationen über Szenarien für die Geschäftskontinuität finden Sie unter [Szenarien für die Geschäftskontinuität](sql-database-business-continuity.md).
* Informationen über automatisierte Sicherungen von Azure SQL-Datenbanken finden Sie unter [automatisierter SQL-Datenbanksicherungen](sql-database-automated-backups.md)
* Informationen zum Verwenden automatisierter Sicherungen für die Wiederherstellung finden Sie unter [Wiederherstellen einer Datenbank aus vom Dienst initiierten Sicherungen](sql-database-recovery-using-backups.md).
* Informationen zu schnelleren Wiederherstellungsoptionen finden Sie unter [Aktive Georeplikation](sql-database-active-geo-replication.md) und [Autofailover-Gruppen](sql-database-auto-failover-group.md).
