---
title: Übersicht über die Geschäftskontinuität mit Azure Database for MariaDB
description: Übersicht über die Geschäftskontinuität mit Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 4c64f920bf56195ad53ac8acbf3f9199090f0a8b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61043015"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mariadb"></a>Übersicht über die Geschäftskontinuität mit Azure Database for MariaDB

Diese Übersicht beschreibt die Funktionen, die Azure Database for MariaDB für Geschäftskontinuität und Notfallwiederherstellung bereitstellt. Erfahren Sie etwas über Optionen für die Wiederherstellung nach Störungen, die Datenverluste nach sich ziehen oder dazu führen können, dass Ihre Datenbank und Ihre Anwendungen nicht mehr verfügbar sind. Erfahren Sie, was zu tun ist, wenn ein Benutzer- oder Anwendungsfehler die Datenintegrität gefährdet, eine Azure-Region ausfällt oder Wartungsaufgaben für Ihre Anwendung ausgeführt werden müssen.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Features zum Sicherstellen der Geschäftskontinuität

Azure Database for MariaDB bietet Features für Geschäftskontinuität, die automatisierte Sicherungen umfassen und Benutzern die Möglichkeit geben, eine Geowiederherstellung zu initiieren. Jedes Feature weist unterschiedliche Eigenschaften für die geschätzte Wiederherstellungszeit (Estimated Recovery Time, ERT) sowie für mögliche Datenverluste auf. Wenn Sie diese Optionen kennen, können Sie die richtigen Optionen auswählen und in unterschiedlichen Szenarien auch miteinander kombinieren. Wenn Sie Ihren Plan für die Geschäftskontinuität entwickeln, müssen Sie ermitteln, wie viel Zeit maximal vergehen darf, bis die Anwendung nach einer Störung vollständig wiederhergestellt ist – diese Zeitspanne ist Ihre RTO (Recovery Time Objective). Sie müssen auch herausfinden, wie viele kürzlich durchgeführte Datenupdates (in einem bestimmten Zeitraum) verloren gehen dürfen, wenn die Anwendung nach einer Störung wiederhergestellt wird – diese Zeitspanne ist Ihre RPO (Recovery Point Objective).

Die folgende Tabelle vergleicht ERT und RPO für die verfügbaren Features:

| **Funktion** | **Basic** | **Allgemeiner Zweck** | **Arbeitsspeicheroptimiert** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Point-in-Time-Wiederherstellung von Sicherung | Beliebiger Wiederherstellungspunkt innerhalb der Aufbewahrungsdauer | Beliebiger Wiederherstellungspunkt innerhalb der Aufbewahrungsdauer | Beliebiger Wiederherstellungspunkt innerhalb der Aufbewahrungsdauer |
| Geowiederherstellung von georeplizierten Sicherungen | Nicht unterstützt | ERT < 12 Stunden<br/>RPO < 1 Stunde | ERT < 12 Stunden<br/>RPO < 1 Stunde |

> [!IMPORTANT]
> Wenn Sie den Server löschen, werden auch alle sich darauf befindenden Datenbanken gelöscht, und können nicht wiederhergestellt werden. Es ist nicht möglich, einen gelöschten Server wiederherzustellen.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Wiederherstellen eines Servers nach einem Benutzer- oder Anwendungsfehler

Sie können die Sicherungen des Diensts verwenden, um einen Server nach verschiedenen Störungen wiederherzustellen. Es kann passieren, dass ein Benutzer versehentlich Daten, eine wichtige Tabelle oder sogar eine ganze Datenbank löscht. Es kann auch vorkommen, dass eine Anwendung aufgrund eines Anwendungsfehlers unbeabsichtigt gültige Daten mit ungültigen Daten überschreibt usw.

Sie können eine Point-in-Time-Wiederherstellung ausführen, um eine Kopie Ihres Servers zu einem als fehlerfrei bekannten Zeitpunkt zu erstellen. Dieser Zeitpunkt muss innerhalb der Aufbewahrungszeit für Sicherungen liegen, die Sie für den Server konfiguriert haben. Nach der Wiederherstellung der Daten auf dem neuen Server können Sie entweder den ursprünglichen Server durch den wiederhergestellten Server ersetzen oder die benötigten Daten vom wiederhergestellten Server auf den ursprünglichen Server kopieren.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Wiederherstellen nach einem Ausfall des regionalen Azure-Rechenzentrums

Es kommt zwar sehr selten vor, aber es ist möglich, dass ein Azure-Rechenzentrum ausfällt. Ein Ausfall kann den Geschäftsbetrieb einige wenige Minuten oder auch mehrere Stunden unterbrechen.

Eine Möglichkeit ist, einfach zu warten, bis der Server wieder online ist, wenn der Rechenzentrumsausfall behoben wurde. Dies funktioniert bei Anwendungen, bei denen der Server für eine längere Zeit offline sein kann, z.B. in einer Entwicklungsumgebung. Wenn ein Rechenzentrum ausfällt, wissen Sie nicht, wie lange der Ausfall dauern kann, daher ist diese Option nur dann in Erwägung zu ziehen, wenn Sie Ihren Server eine Zeit lang nicht benötigen.

Die andere Option ist die Verwendung des Geowiederherstellungsfeatures von Azure Database for MariaDB, das den Server mithilfe von georedundanten Sicherungen wiederherstellt. Auf diese Sicherungen kann selbst dann zugegriffen werden, wenn die Region, in der Ihr Server gehostet wird, offline ist. Sie können eine Wiederherstellung aus diesen Sicherungen in eine andere Region ausführen und den Server wieder online schalten.

> [!IMPORTANT]
> Die Geowiederherstellung ist nur möglich, wenn Sie den Server mit einem georedundanten Sicherungsspeicher bereitgestellt haben.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu automatisierten Sicherungen finden Sie unter [Sicherungen in Azure Database for MariaDB](concepts-backup.md).
- Informationen zur Wiederherstellung des Zustands zu einem bestimmten Zeitpunkt über das Azure-Portal finden Sie unter  [Sichern und Wiederherstellen eines Servers in Azure Database for MySQL mit dem Azure-Portal](howto-restore-server-portal.md).

<!--
- To restore to a point in time using Azure CLI, see [restore database to a point in time using CLI](howto-restore-server-cli.md). 
-->
