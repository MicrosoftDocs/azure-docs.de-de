---
title: 'Planen von Azure-Wartungsereignissen: Azure SQL-Datenbank | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie sich auf geplante Wartungsereignisse für Ihre Azure SQL-Datenbank vorbereiten.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
manager: craigg
ms.date: 01/30/2019
ms.openlocfilehash: 928338a911efae051df7164239dbd19f9317338a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60584606"
---
# <a name="planning-for-azure-maintenance-events-in-azure-sql-database"></a>Planen von Azure-Wartungsereignissen in Azure SQL-Datenbank

Erfahren Sie, wie Sie sich auf geplante Wartungsereignisse für Ihre Azure SQL-Datenbank vorbereiten.

## <a name="what-is-a-planned-maintenance-event"></a>Was ist ein geplantes Wartungsereignis?

Für jede Datenbank verwaltet Azure SQL-Datenbank ein Quorum von Datenbankreplikaten, von denen ein Replikat das primäre Replikat ist. Zu jeder Zeit muss ein primäres Replikat online in Betrieb sein, und mindestens ein sekundäres Replikat muss integer sein. Während der geplanten Wartung werden die Mitglieder des Datenbankquorums nacheinander mit der Absicht offline geschaltet, dass ein antwortendes primäres Replikat und mindestens ein sekundäres Replikat online vorhanden ist, um sicherzustellen, dass es keine Ausfallzeiten des Clients auftreten. Wenn das primäre Replikat offline geschaltet werden muss, erfolgt ein Neukonfigurations-/Failoverprozess, bei dem ein sekundäres Replikat zum neuen primären Replikat wird.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Was Sie bei einem geplanten Wartungsereignis erwarten können

Neukonfigurationen/Failover werden in der Regel innerhalb von 30 Sekunden abgeschlossen. Der Durchschnittswert sind 8 Sekunden. Wenn sie bereits verbunden ist, muss Ihre Anwendung mit der integren Kopie des neuen primären Replikats Ihrer Datenbank erneut eine Verbindung herstellen. Wenn eine neue Verbindung versucht wird, während für die Datenbank eine Neukonfiguration durchgeführt wird, bevor das neue primäre Replikat online ist, erhalten Sie Fehler 40613 (Datenbank nicht verfügbar): „Datenbank '{Datenbankname}' auf Server '{Servername}' ist zurzeit nicht verfügbar. Wiederholen Sie den Verbindungsversuch später.“ Wenn Ihre Datenbank eine zeitintensive Abfrage aufweist, wird diese Abfrage während einer Neukonfiguration unterbrochen und muss neu gestartet werden.

## <a name="retry-logic"></a>Wiederholungslogik

Jede Clientproduktionsanwendung, die eine Verbindung mit einem Clouddatenbankdienst herstellt, sollte eine stabile [Wiederholungslogik](sql-database-connectivity-issues.md#retry-logic-for-transient-errors) für Verbindungen implementieren. Dies trägt zur Lösung dieser Situationen bei und sollte normalerweise dazu führen, dass die Fehler für den Endbenutzer transparent sind.

## <a name="frequency"></a>Frequency

Durchschnittlich sollten 1,7 geplante Wartungsereignisse pro Monat auftreten.

## <a name="resource-health"></a>Resource Health

Wenn für Ihre SQL-Datenbank Anmeldefehler auftreten, überprüfen Sie im Fenster [Resource Health](../service-health/resource-health-overview.md#getting-started) im [Azure-Portal](https://portal.azure.com) den aktuellen Status. Der Abschnitt „Integritätsverlauf“ enthält den Grund für die Ausfallzeit für jedes Ereignis (wenn verfügbar).


## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Resource Health](sql-database-resource-health.md) für SQL-Datenbank
- Weitere Informationen zur Wiederholungslogik finden Sie unter [Wiederholungslogik für vorübergehende Fehler](sql-database-connectivity-issues.md#retry-logic-for-transient-errors).
