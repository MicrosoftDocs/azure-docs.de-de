---
title: Überwachen der Datenbankintegrität mithilfe von Azure Resource Health
description: Mithilfe von Azure Resource Health können Sie die Integrität von Azure SQL-Datenbank und der verwalteten Azure SQL-Instanz überwachen, um Probleme zu diagnostizieren und Unterstützung zu erhalten, wenn sich ein Azure-bezogenes Problem auf Ihre SQL-Ressourcen auswirkt.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: wiassaf, sstein
ms.date: 02/26/2019
ms.openlocfilehash: a51840daa2c8b19c323ad761ff6e6d1dcef15f75
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96497961"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database-and-azure-sql-managed-instance"></a>Behandeln von Konnektivitätsproblemen für Azure SQL-Datenbank und verwalteten Azure SQL-Instanzen mithilfe von Resource Health
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Mit [Resource Health](../../service-health/resource-health-overview.md#get-started) können Sie die Integrität von Azure SQL-Datenbank und Azure SQL Managed Instance diagnostizieren und Unterstützung erhalten, wenn sich ein Azure-bezogenes Problem auf Ihre SQL-Ressourcen auswirkt. Der Dienst informiert Sie über die aktuelle und frühere Integrität Ihrer Ressourcen und unterstützt Sie beim Beheben von Problemen. Resource Health bietet technischen Support, wenn Sie Unterstützung bei Azure-Dienstproblemen benötigen.

![Übersicht](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Integritätsüberprüfungen

Resource Health ermittelt anhand von erfolgreichen und nicht erfolgreichen Anmeldungen die Integrität Ihrer SQL-Ressource. Aktuell untersucht Resource Health für Ihre SQL-Datenbankressource nur Anmeldefehler, die auf Systemfehler (nicht auf Benutzerfehler) zurückzuführen sind. Der Resource Health-Status wird im Abstand von ein bis zwei Minuten aktualisiert.

## <a name="health-states"></a>Integritätszustände

### <a name="available"></a>Verfügbar

Der Status **Verfügbar** bedeutet, dass Resource Health für Ihre SQL-Ressource keine Anmeldefehler erkannt hat, die auf Systemfehler zurückzuführen sind.

![Verfügbar](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-available.jpg)

### <a name="degraded"></a>Heruntergestuft

Der Status **Beeinträchtigt** bedeutet, dass der Großteil der Anmeldungen erfolgreich war, bei einigen aber auch ein Fehler aufgetreten ist. Dabei handelt es sich wahrscheinlich um vorübergehende Anmeldefehler. Implementieren Sie in Ihrem Code eine [Wiederholungslogik](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors), um die Auswirkungen von Verbindungsproblemen zu verringern, die durch vorübergehende Anmeldefehler verursacht werden.

![Heruntergestuft](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Nicht verfügbar

Der Status **Nicht verfügbar** bedeutet, dass Resource Health konstant Anmeldefehler für Ihre SQL-Ressource erkannt hat. Falls sich Ihre Ressource länger in diesem Zustand befindet, wenden Sie sich an den Support.

![Nicht verfügbar](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Unknown

Der Integritätsstatus **Unbekannt** gibt an, dass Resource Health seit mehr als 10 Minuten keine Informationen mehr zu dieser Ressource empfangen hat. Dieser Status ist zwar keine definitive Angabe des Ressourcenzustands, aber ein wichtiger Datenpunkt im Problembehandlungsprozess. Wenn die Ressource wie erwartet ausgeführt wird, wird der Status der Ressource nach wenigen Minuten in „Verfügbar“ geändert. Wenn Probleme mit der Ressource auftreten, kann der Integritätsstatus „Unbekannt“ darauf hindeuten, dass die Ressource durch ein Ereignis auf der Plattform beeinträchtigt wird.

![Unknown](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Verlaufsinformationen

Im Abschnitt „Integritätsverlauf“ von Resource Health stehen bis zu 14 Tage alte Integritätsverlaufsdaten zur Verfügung. Dieser Abschnitt enthält auch die Ursache für von Resource Health gemeldete Downtime (sofern verfügbar). Derzeit zeigt Azure die Downtime für Ihre Datenbankressource auf zwei Minuten genau an. Die tatsächliche Downtime beträgt wahrscheinlich weniger als einer Minute. Die durchschnittliche Dauer beträgt 8 Sekunden.

### <a name="downtime-reasons"></a>Ursachen für Downtime

Bei einer Downtime Ihrer Datenbank wird eine Analyse durchgeführt, um eine Ursache zu ermitteln. Die Ursache der Downtime wird (sofern verfügbar) im Abschnitt „Integritätsverlauf“ von Resource Health gemeldet. Ursachen für Downtime werden in der Regel 30 Minuten nach einem Ereignis veröffentlicht.

#### <a name="planned-maintenance"></a>Geplante Wartung

Für die Azure-Infrastruktur werden in regelmäßigen Abständen geplante Wartungsmaßnahmen (Hardware- oder Softwareupgrades im Rechenzentrum) durchgeführt. Während die Datenbank gewartet wird, werden von Azure SQL unter Umständen einige bestehende Verbindungen getrennt und neue Verbindungen abgelehnt. Anmeldefehler während einer geplanten Wartung sind in der Regel vorübergehend, und deren Auswirkungen lassen sich durch eine [Wiederholungslogik](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) reduzieren. Sollten weiterhin Anmeldefehler auftreten, wenden Sie sich an den Support.

#### <a name="reconfiguration"></a>Reconfiguration

Neukonfigurationen gelten als vorübergehende Bedingungen und werden von Zeit zu Zeit erwartet. Diese Ereignisse können durch den Lastenausgleich oder durch Software-/Hardwarefehler ausgelöst werden. Jede Clientproduktionsanwendung, die eine Verbindung mit einer Clouddatenbank herstellt, sollte eine stabile [Wiederholungslogik](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) für Verbindungen implementieren, da dies dazu beiträgt, die Auswirkungen solcher Situationen zu verringern und die Fehler für den Endbenutzer transparent zu machen.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich ausführlicher über [Wiederholungslogik für vorübergehende Fehler](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors).
- Machen Sie sich mit [Durchführen der Problembehandlung, Diagnose und Verhinderung von SQL-Verbindungsfehlern und vorübergehenden Fehlern für SQL-Datenbank](troubleshoot-common-connectivity-issues.md) vertraut.
- Erfahren Sie mehr über das [Konfigurieren von Resource Health-Warnungen](../../service-health/resource-health-alert-arm-template-guide.md).
- Verschaffen Sie sich einen Überblick über [Resource Health](../../application-gateway/resource-health-overview.md).
- Lesen Sie [Azure Resource Health – FAQ](../../service-health/resource-health-faq.md).
