---
title: 'Tutorial: Georeplikation und Failover im Portal'
description: Enthält Informationen zum Konfigurieren der Georeplikation für eine Datenbank im Azure-Portal und zum Initiieren eines Failovers.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 02/13/2019
ms.openlocfilehash: 71c73fec4f559b34b097556243617636acd77480
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92673261"
---
# <a name="tutorial-configure-active-geo-replication-and-failover-in-the-azure-portal-azure-sql-database"></a>Tutorial: Konfigurieren der aktiven Georeplikation und des Failovers im Azure-Portal (Azure SQL-Datenbank)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In diesem Artikel wird veranschaulicht, wie Sie die [aktive Georeplikation für Azure SQL-Datenbank](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities) im [Azure-Portal](https://portal.azure.com) konfigurieren und ein Failover initiieren.

Bewährte Methoden für die Verwendung von Autofailover-Gruppen finden Sie unter [Bewährte Methoden für Azure SQL-Datenbank](auto-failover-group-overview.md#best-practices-for-sql-database) und [Bewährte Methoden für verwaltete Azure SQL-Instanz](auto-failover-group-overview.md#best-practices-for-sql-managed-instance). 



## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie die aktive Georeplikation über das Azure-Portal konfigurieren möchten, benötigen Sie folgende Ressource:

* Eine Datenbank in Azure SQL-Datenbank: Die primäre Datenbank, die in eine andere geografische Region repliziert werden soll.

> [!Note]
> Wenn Sie das Azure-Portal verwenden, können Sie nur eine sekundäre Datenbank im selben Abonnement wie die primäre Datenbank erstellen. Wenn eine sekundäre Datenbank in einem anderen Abonnement vorhanden sein muss, verwenden Sie die [REST-API zum Erstellen der Datenbank](/rest/api/sql/databases/createorupdate) oder die [ALTER DATABASE-Transact-SQL-API](/sql/t-sql/statements/alter-database-transact-sql).

## <a name="add-a-secondary-database"></a>Hinzufügen einer sekundären Datenbank

Mit den folgenden Schritten wird eine neue sekundäre Datenbank in einer Partnerschaft für die Georeplikation erstellt.  

Zum Hinzufügen einer sekundären Datenbank müssen Sie der Besitzer oder Mitbesitzer des Abonnements sein.

Die sekundäre Datenbank hat den gleichen Namen wie die primäre Datenbank und standardmäßig auch die gleiche Dienstebene und Computegröße. Die sekundäre Datenbank kann eine Einzel- oder Pooldatenbank sein. Weitere Informationen finden Sie unter [DTU-basiertes Kaufmodell](service-tiers-dtu.md) und [vCore-basiertes Kaufmodell](service-tiers-vcore.md).
Nachdem die sekundäre Datenbank erstellt und das Seeding ausgeführt wurde, beginnt die Replikation der Daten von der primären Datenbank in die neue sekundäre Datenbank.

> [!NOTE]
> Wenn die Partnerdatenbank bereits vorhanden ist (z.B. aufgrund der Beendigung einer vorherigen Georeplikationsbeziehung), tritt für den Befehl ein Fehler auf.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu der Datenbank, die Sie für die Georeplikation einrichten möchten.
2. Wählen Sie auf der Seite „SQL-Datenbank“ die Option **Georeplikation** und dann die Region aus, in der die sekundäre Datenbank erstellt werden soll. Sie können jede Region außer der Region auswählen, in der die primäre Datenbank gehostet wird. Es empfiehlt sich jedoch, die Partnerregion im [Regionspaar](../../best-practices-availability-paired-regions.md) zu verwenden.

    ![Konfigurieren der Georeplikation](./media/active-geo-replication-configure-portal/configure-geo-replication.png)
3. Wählen Sie den Server und den Tarif für die sekundäre Datenbank aus bzw. konfigurieren Sie Server und Tarif.

    ![Dialogfeld „Sekundäre Datenbank erstellen“](./media/active-geo-replication-configure-portal/create-secondary.png)
4. Optional können Sie einem Pool für elastische Datenbanken eine sekundäre Datenbank hinzufügen. Um die sekundäre Datenbank in einem Pool zu erstellen, klicken Sie auf **Pool für elastische Datenbanken** , und wählen Sie einen Pool auf dem Zielserver aus. Ein Pool muss bereits auf dem Zielserver vorhanden sein. Dieser Workflow erstellt keinen Pool.
5. Klicken Sie auf **Erstellen** , um die sekundäre Datenbank hinzuzufügen.
6. Die sekundäre Datenbank wird erstellt, und der Seedingprozess beginnt.

    ![Karte mit sekundären Datenbanken](./media/active-geo-replication-configure-portal/seeding0.png)
7. Nach Abschluss des Seedingprozesses wird der Status für die zweite Datenbank angezeigt.

    ![Seeding abgeschlossen](./media/active-geo-replication-configure-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Initiieren eines Failovers

Für die sekundäre Datenbank kann ein Wechsel durchgeführt werden, bei dem sie zur primären Datenbank wird.  

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zur primären Datenbank in der Georeplikationspartnerschaft.
2. Wählen Sie auf dem Blatt „SQL-Datenbank“ **Alle Einstellungen** > **Georeplikation** aus.
3. Wählen Sie in der Liste **SEKUNDÄRE DATENBANKEN** die Datenbank aus, die zur neuen primären Datenbank werden soll. Klicken Sie anschließend auf **Erzwungenes Failover** .

    ![failover](./media/active-geo-replication-configure-portal/secondaries.png)
4. Klicken Sie auf **Ja** , um das Failover zu beginnen.

Durch den Befehl wird die sekundäre Datenbank sofort in die primäre Rolle geändert. Dieser Vorgang sollte normalerweise innerhalb von 30 Sekunden oder weniger abgeschlossen sein.

Es gibt einen kurzer Zeitraum, in dem beide Datenbanken während des Rollenwechsels (ca. 0 bis 25 Sekunden) nicht verfügbar sind. Wenn die primäre Datenbank über mehrere sekundäre Datenbanken verfügt, werden die anderen sekundären Datenbanken durch den Befehl automatisch neu konfiguriert, sodass sie eine Verbindung mit der neuen primären Datenbank herstellen. Unter normalen Umständen dauert der gesamte Vorgang nicht länger als 1 Minute.

> [!NOTE]
> Dieser Befehl ist für die schnelle Wiederherstellung der Datenbank bei einem Ausfall bestimmt. Er löst ein Failover ohne Datensynchronisierung aus (erzwungenes Failover).  Wenn die primäre Datenbank bei Ausgabe des Befehls online ist und gerade Commits für Transaktionen ausführt, können einige Daten verloren gehen.

## <a name="remove-secondary-database"></a>Entfernen einer sekundären Datenbank

Dieser Vorgang beendet die Replikation zur sekundären Datenbank dauerhaft und ändert die Rolle der sekundären Datenbank in eine normale Datenbank mit Lese-/Schreibzugriff. Wenn die Verbindung mit der sekundären Datenbank unterbrochen wird, ist der Befehl zwar erfolgreich, aber die sekundäre Datenbank wird erst mit Lese-/ Schreibzugriff versehen, nachdem die Verbindung wiederhergestellt wurde.  

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zur primären Datenbank in der Georeplikationspartnerschaft.
2. Wählen Sie auf der Seite „SQL-Datenbank“ die Option **Georeplikation** aus.
3. Wählen Sie in der Liste **SEKUNDÄRE DATENBANKEN** die Datenbank aus, die Sie aus der Georeplikationspartnerschaft entfernen möchten.
4. Klicken Sie auf **Replikation beenden** .

    ![Sekundäre Datenbank entfernen](./media/active-geo-replication-configure-portal/remove-secondary.png)
5. Ein Bestätigungsfenster wird geöffnet. Klicken Sie zum Entfernen der Datenbank aus der Georeplikationspartnerschaft auf **Ja** . (Setzen Sie sie auf eine Datenbank mit Lese-/ Schreibzugriff fest, die kein Teil der Replikation ist.)

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur aktiven Georeplikation finden Sie unter [Aktive Georeplikation](active-geo-replication-overview.md).
* Informationen zu Autofailover-Gruppen finden Sie unter [Autofailover-Gruppen](auto-failover-group-overview.md).
* Eine Übersicht und verschiedene Szenarien zum Thema Geschäftskontinuität finden Sie unter [Übersicht über die Geschäftskontinuität](business-continuity-high-availability-disaster-recover-hadr-overview.md).