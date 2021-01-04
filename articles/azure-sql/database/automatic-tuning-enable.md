---
title: Aktivieren der automatischen Optimierung
description: Sie können über das Azure-Portal ganz einfach die automatische Optimierung für Ihre Datenbanken aktivieren.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 12/03/2019
ms.openlocfilehash: 35e2a73b0cfae104cee417e7d4a159e7fd169a17
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500902"
---
# <a name="enable-automatic-tuning-in-the-azure-portal-to-monitor-queries-and-improve-workload-performance"></a>Aktivieren der automatischen Optimierung im Azure-Portal zum Überwachen von Abfragen und Verbessern der Workloadleistung
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Azure SQL-Datenbank verwaltet Datendienste automatisch, die kontinuierlich Ihre Abfragen überwachen, und teilt Ihnen mit, wie Sie die Leistung Ihrer Workload verbessern können. Sie können Empfehlungen prüfen und manuell anwenden oder Azure SQL-Datenbank die Maßnahmen automatisch anwenden lassen. Letzteres wird als **automatischer Optimierungsmodus** bezeichnet.

Die automatische Optimierung kann auf Server- oder auf Datenbankebene über eine der folgenden Optionen aktiviert werden:

- Das [Azure-Portal](automatic-tuning-enable.md#azure-portal)
- [REST-API](automatic-tuning-enable.md#rest-api)-Aufrufe
- [T-SQL](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true)-Befehle

> [!NOTE]
> Für Azure SQL Managed Instance kann die unterstützte Option FORCE_LAST_GOOD_PLAN nur über [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) konfiguriert werden. Die in diesem Artikel beschriebene auf dem Azure-Portal basierende Konfiguration und die Optionen zur automatischen Indexoptimierung gelten nicht für Azure SQL Managed Instance.

> [!NOTE]
> Die Vorlage zum Konfigurieren der Optionen zur automatischen Optimierung über ARM (Azure Resource Manager) wird derzeit nicht unterstützt.

## <a name="enable-automatic-tuning-on-server"></a>Aktivieren der automatischen Optimierung für den Server

Auf Serverebene kann auf Wunsch die Konfiguration der automatischen Optimierung von „Azure-Standardwerte“ geerbt werden. In den Azure-Standardwerten ist FORCE_LAST_GOOD_PLAN aktiviert, CREATE_INDEX und DROP_INDEX sind deaktiviert.

> [!IMPORTANT]
> Ab März 2020 lauten die neuen Standardwerte für die automatische Optimierung in Azure wie folgt:
>
> - FORCE_LAST_GOOD_PLAN = aktiviert, CREATE_INDEX = deaktiviert und DROP_INDEX = deaktiviert.
> - Vorhandene Server ohne konfigurierte automatische Optimierungseinstellungen werden automatisch so konfiguriert, dass sie die neuen Azure-Standardeinstellungen ERBEN. Dies gilt für alle Kunden, bei denen sich die automatische Optimierung von Servereinstellungen in einem nicht definierten Zustand befindet.
> - Neu erstellte Server werden automatisch so konfiguriert, dass sie die neuen Azure-Standardeinstellungen ERBEN. Bisher befand sich die automatische Optimierungskonfiguration bei der Erstellung neuer Server in einem nicht definierten Zustand.

### <a name="azure-portal"></a>Azure-Portal

Wenn Sie die automatische Optimierung für einen [Server](logical-servers.md) in Azure SQL-Datenbank aktivieren möchten, navigieren Sie im Azure-Portal zum entsprechenden Server, und klicken Sie im Menü auf die Option **Automatische Optimierung**.

![Screenshot zeigt „Automatische Optimierung“ im Azure-Portal, wo Sie Optionen für einen Server anwenden können.](./media/automatic-tuning-enable/server.png)

> [!NOTE]
> Beachten Sie, dass die Option **DROP_INDEX** zurzeit nicht kompatibel mit Anwendungen ist, die Partitionswechsel und Indexhinweise verwenden. In diesen Fällen sollte sie nicht aktiviert werden. Das Löschen nicht verwendeter Indizes wird bei den Dienstebenen „Premium“ und „Unternehmenskritisch“ nicht unterstützt.

Wählen Sie die gewünschten Optionen für die automatische Optimierung und anschließend **Übernehmen** aus.

Die Optionen für die automatische Optimierung auf einem Server werden auf alle Datenbanken auf diesem Server angewendet. Standardmäßig erben alle Datenbanken die Konfiguration von ihrem übergeordneten Server. Dies kann jedoch außer Kraft gesetzt und einzeln für jede Datenbank angegeben werden.

### <a name="rest-api"></a>REST-API

Weitere Informationen darüber, wie die automatische Optimierung mithilfe der REST-API für einen **Server** aktiviert werden kann, finden Sie unter [Automatische Serveroptimierung](/rest/api/sql/serverautomatictuning).

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Aktivieren der automatischen Optimierung für eine einzelne Datenbank

Über Azure SQL-Datenbank können Sie die Konfiguration der automatischen Optimierung für einzelne Datenbanken angeben. Auf Datenbankebene kann auf Wunsch die Konfiguration der automatischen Optimierung vom übergeordneten Server geerbt („Azure-Standardwerte“) oder nicht geerbt werden. Die Azure-Standardwerte sind wie folgt festgelegt: FORCE_LAST_GOOD_PLAN ist aktiviert, CREATE_INDEX und DROP_INDEX sind deaktiviert.

> [!TIP]
> Es empfiehlt sich im Allgemeinen, die automatische Optimierung auf **Serverebene** zu verwalten, damit für jede Datenbank automatisch die gleichen Konfigurationseinstellungen angewendet werden können. Konfigurieren Sie die automatische Optimierung für eine einzelne Datenbank nur dann, wenn diese Datenbank andere Einstellungen haben muss als die anderen Datenbanken, die ihre Einstellungen vom gleichen Server erben.

### <a name="azure-portal"></a>Azure-Portal

Wenn Sie die automatische Optimierung für ein **Singleton** aktivieren möchten, navigieren Sie im Azure-Portal zur entsprechenden Datenbank, und klicken Sie auf **Automatische Optimierung**.

Einzelne Einstellungen für die automatische Optimierung können für jede Datenbank separat konfiguriert werden. Sie können manuell eine einzelne automatische Optimierungsoption konfigurieren oder angeben, dass eine Option die Einstellungen vom Server erbt.

![Screenshot zeigt „Automatische Optimierung“ im Azure-Portal, wo Sie Optionen für eine einzelne Datenbank anwenden können.](./media/automatic-tuning-enable/database.png)

Beachten Sie, dass die Option DROP_INDEX zurzeit nicht kompatibel mit Anwendungen ist, die Partitionswechsel und Indexhinweise verwenden. In diesen Fällen sollte sie nicht aktiviert werden.

Klicken Sie nach der Auswahl der gewünschten Konfiguration auf **Übernehmen**.

### <a name="rest-api"></a>REST-API

Weitere Informationen darüber, wie die automatische Optimierung mithilfe der REST-API in einem Singleton aktiviert werden kann, finden Sie unter [Automatische Serveroptimierung](/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

Wenn Sie die automatische Optimierung für eine einzelne Datenbank mithilfe von T-SQL aktivieren möchten, stellen Sie eine Verbindung mit der Datenbank her, und führen Sie die folgende Abfrage aus:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

Wenn Sie die automatische Optimierung auf „AUTO“ festlegen, werden die Azure-Standardwerte verwendet. Bei Verwendung von „INHERIT“ wird die Konfiguration der automatischen Optimierung vom übergeordneten Server geerbt. Bei Verwendung von „CUSTOM“ muss die automatische Optimierung manuell konfiguriert werden.

Wenn Sie über T-SQL einzelne Optionen der automatischen Optimierung konfigurieren möchten, stellen Sie eine Verbindung mit der Datenbank her, und führen Sie eine Abfrage wie die folgende aus:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = ON, DROP_INDEX = OFF)
```

Wenn Sie eine Optimierungsoption auf „ON“ festlegen, werden gegebenenfalls geerbte Einstellungen der Datenbank überschrieben, und die entsprechende Optimierungsoption wird aktiviert. Gleiches gilt für „OFF“: Auch hier werden gegebenenfalls geerbte Einstellungen der Datenbank überschrieben, und die entsprechende Optimierungsoption wird deaktiviert. Bei Verwendung der automatischen Optimierungsoption „DEFAULT“ wird die Konfiguration für automatische Optimierung aus den Einstellungen auf Serverebene geerbt.  

> [!IMPORTANT]
> Im Fall einer [aktiven Georeplikation](auto-failover-group-overview.md) muss die automatische Optimierung nur für die primäre Datenbank konfiguriert werden. Automatisch angewandte Aktionen zur Optimierung, z. B. das Erstellen oder Löschen des Index, werden automatisch in die schreibgeschützte sekundäre Datenbank repliziert. Der Versuch, die automatischen Optimierung in der sekundären schreibgeschützten Datenbank über T-SQL zu aktivieren, führt zu einem Fehler, da für die schreibgeschützte sekundäre Datenbank keine abweichende Optimierungskonfiguration unterstützt wird.
>

Informationen zu den T-SQL-Optionen für die Konfiguration der automatischen Optimierung finden Sie unter [ALTER DATABASE SET-Optionen (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true).

## <a name="disabled-by-the-system"></a>Vom System deaktiviert

Die automatische Optimierung überwacht alle Aktionen, die sie für die Datenbank ausführt, und stellt manchmal unter Umständen fest, dass die automatische Optimierung für die Datenbank nicht ordnungsgemäß funktioniert. In diesem Fall wird die Optimierungsoption vom System deaktiviert. Das liegt meistens daran, dass der Abfragespeicher nicht aktiviert oder für eine bestimmte Datenbank schreibgeschützt ist.

## <a name="permissions"></a>Berechtigungen

Da die automatische Optimierung ein Azure-Feature ist, müssen Sie für deren Nutzung die integrierten Rollen von Azure verwenden. Die ausschließliche Verwendung der SQL-Authentifizierung reicht nicht aus, um das Feature über das Azure-Portal zu nutzen.

Zur Verwendung der automatischen Optimierung muss dem Benutzer mindestens die Berechtigung der integrierten Azure-Rolle [SQL-DB-Mitwirkender](../../role-based-access-control/built-in-roles.md#sql-db-contributor) erteilt werden. Sie können auch Rollen mit höheren Berechtigungen verwenden, z. B. SQL Server-Mitwirkender, Mitwirkender und Besitzer.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Konfigurieren der automatischen Optimierung von E-Mail-Benachrichtigungen

Weitere Informationen hierzu finden Sie im Leitfaden [E-Mail-Benachrichtigungen zur automatischen Optimierung](automatic-tuning-email-notifications-configure.md).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur automatischen Optimierung sowie zu ihrer Rolle bei der Verbesserung der Leistung finden Sie in [diesem Artikel](automatic-tuning-overview.md).
- Eine Übersicht über die Leistungsempfehlungen von Azure SQL-Datenbank finden Sie [hier](database-advisor-implement-performance-recommendations.md).
- Unter [Query Performance Insight](query-performance-insight-use.md) erfahren Sie, wie Sie die Auswirkungen Ihrer wichtigsten Abfragen auf die Leistung untersuchen können.
