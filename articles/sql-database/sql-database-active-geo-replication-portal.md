---
title: 'Azure-Portal: SQL-Datenbank – Georeplikation | Microsoft-Dokumentation'
description: Konfigurieren der Georeplikation für eine Einzel- oder Pooldatenbank in Azure SQL-Datenbank über das Azure-Portal und Initiieren eines Failovers
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
ms.openlocfilehash: 8bada96c648881a9943176c45115627a829fcc58
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60864059"
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Konfigurieren der aktiven Georeplikation für Azure SQL-Datenbank im Azure-Portal und Initiieren eines Failovers

In diesem Artikel erfahren Sie, wie Sie die [aktive Georeplikation für Einzel- und Pooldatenbanken](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities) in Azure SQL-Datenbank mithilfe des [Azure-Portals](https://portal.azure.com) konfigurieren und ein Failover initiieren.

Weitere Informationen zu Autofailover-Gruppen mit Einzel- oder Pooldatenbanken finden Sie unter [Bewährte Methoden der Verwendung von Failovergruppen für Einzel- und Pooldatenbanken](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). Weitere Informationen zu Autofailover-Gruppen mit verwalteten Instanzen (Vorschau) finden Sie unter [Bewährte Methoden der Verwendung von Failovergruppen mit verwalteten Instanzen](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-managed-instances).

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie die aktive Georeplikation über das Azure-Portal konfigurieren möchten, benötigen Sie folgende Ressource:

* Eine Azure SQL-Datenbank: Die primäre Datenbank, die in eine andere geografische Region repliziert werden soll.

> [!Note]
> Wenn Sie das Azure-Portal verwenden, können Sie nur eine sekundäre Datenbank im selben Abonnement wie die primäre Datenbank erstellen. Wenn die sekundäre Datenbank in einem anderen Abonnement vorhanden sein muss, verwenden Sie die [REST-API zum Erstellen der Datenbank](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) oder die [ALTER DATABASE-Transact-SQL-API](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql).

## <a name="add-a-secondary-database"></a>Hinzufügen einer sekundären Datenbank

Mit den folgenden Schritten wird eine neue sekundäre Datenbank in einer Partnerschaft für die Georeplikation erstellt.  

Zum Hinzufügen einer sekundären Datenbank müssen Sie der Besitzer oder Mitbesitzer des Abonnements sein.

Die sekundäre Datenbank hat den gleichen Namen wie die primäre Datenbank und standardmäßig auch die gleiche Dienstebene und Computegröße. Die sekundäre Datenbank kann eine Einzel- oder Pooldatenbank sein. Weitere Informationen finden Sie unter [DTU-basiertes Kaufmodell](sql-database-service-tiers-dtu.md) und [vCore-basiertes Kaufmodell](sql-database-service-tiers-vcore.md).
Nachdem die sekundäre Datenbank erstellt und das Seeding ausgeführt wurde, beginnt die Replikation der Daten von der primären Datenbank in die neue sekundäre Datenbank.

> [!NOTE]
> Wenn die Partnerdatenbank bereits vorhanden ist (z.B. aufgrund der Beendigung einer vorherigen Georeplikationsbeziehung), tritt für den Befehl ein Fehler auf.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu der Datenbank, die Sie für die Georeplikation einrichten möchten.
2. Wählen Sie auf der Seite „SQL-Datenbank“ die Option **Georeplikation**, und wählen Sie dann die Region aus, in der die sekundäre Datenbank erstellt werden soll. Sie können jede Region außer der Region auswählen, in der die primäre Datenbank gehostet wird. Es empfiehlt sich jedoch, die Partnerregion im [Regionspaar](../best-practices-availability-paired-regions.md) zu verwenden.

    ![Konfigurieren der Georeplikation](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Wählen Sie den Server und den Tarif für die sekundäre Datenbank aus bzw. konfigurieren Sie Server und Tarif.

    ![Konfigurieren der sekundären Datenbank](./media/sql-database-geo-replication-portal/create-secondary.png)
4. Optional können Sie einem Pool für elastische Datenbanken eine sekundäre Datenbank hinzufügen. Um die sekundäre Datenbank in einem Pool zu erstellen, klicken Sie auf **Pool für elastische Datenbanken**, und wählen Sie einen Pool auf dem Zielserver aus. Ein Pool muss bereits auf dem Zielserver vorhanden sein. Dieser Workflow erstellt keinen Pool.
5. Klicken Sie auf **Erstellen** , um die sekundäre Datenbank hinzuzufügen.
6. Die sekundäre Datenbank wird erstellt, und der Seedingprozess beginnt.

    ![Konfigurieren der sekundären Datenbank](./media/sql-database-geo-replication-portal/seeding0.png)
7. Nach Abschluss des Seedingprozesses wird der Status für die zweite Datenbank angezeigt.

    ![Seeding abgeschlossen](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Initiieren eines Failovers

Für die sekundäre Datenbank kann ein Wechsel durchgeführt werden, bei dem sie zur primären Datenbank wird.  

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zur primären Datenbank in der Georeplikationspartnerschaft.
2. Wählen Sie auf dem Blatt „SQL-Datenbank“ **Alle Einstellungen** > **Georeplikation** aus.
3. Wählen Sie in der Liste **SEKUNDÄRE DATENBANKEN** die Datenbank aus, die zur neuen primären Datenbank werden soll. Klicken Sie anschließend auf **Failover**.

    ![Failover](./media/sql-database-geo-replication-failover-portal/secondaries.png)
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
4. Klicken Sie auf **Replikation beenden**.

    ![Sekundäre Datenbank entfernen](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Ein Bestätigungsfenster wird geöffnet. Klicken Sie zum Entfernen der Datenbank aus der Georeplikationspartnerschaft auf **Ja**. (Setzen Sie sie auf eine Datenbank mit Lese-/ Schreibzugriff fest, die kein Teil der Replikation ist.)

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur aktiven Georeplikation finden Sie unter [Aktive Georeplikation](sql-database-active-geo-replication.md).
* Informationen zu Autofailover-Gruppen finden Sie unter [Autofailover-Gruppen](sql-database-auto-failover-group.md).
* Eine Übersicht und verschiedene Szenarien zum Thema Geschäftskontinuität finden Sie unter [Übersicht über die Geschäftskontinuität](sql-database-business-continuity.md).
