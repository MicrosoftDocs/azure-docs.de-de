---
title: Verschieben von Azure SQL-Ressourcen zwischen Regionen mit Azure Resource Mover
description: Erfahren Sie, wie Sie Azure SQL-Ressourcen mit Azure Resource Mover in eine andere Region verschieben.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9fe43125c83436f89bf93cbe975317efec2beb46
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95542812"
---
# <a name="tutorial-move-azure-sql-database-resources-to-another-region"></a>Tutorial: Verschieben von Azure SQL-Datenbankressourcen in eine andere Region

In diesem Tutorial erfahren Sie, wie Sie Azure SQL-Datenbanken und Pools für elastische Datenbanken mithilfe von [Azure Resource Mover](overview.md) in eine andere Azure-Region verschieben.

> [!NOTE]
> Azure Resource Mover ist derzeit als Vorschauversion verfügbar.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Prüfen der Voraussetzungen und Anforderungen
> * Auswählen der Ressourcen, die Sie verschieben möchten
> * Auflösen von Ressourcenabhängigkeiten
> * Vorbereiten von SQL Server und Verschieben in die Zielregion
> * Vorbereiten und Verschieben von Datenbanken und Pools für elastische Datenbanken
> * Verwerfen oder Committen der Verschiebung 
> * Optionales Entfernen von Ressourcen in der Quellregion nach der Verschiebung 

> [!NOTE]
> Tutorials zeigen den schnellsten Weg zum Ausprobieren eines Szenarios, dabei kommen die Standardoptionen zum Einsatz. 

Wenn Sie kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/), bevor Sie beginnen. Melden Sie sich dann beim [Azure-Portal](https://portal.azure.com)an.

## <a name="prerequisites"></a>Voraussetzungen

-  Überprüfen Sie, ob Sie *Besitzerzugriff* auf das Abonnement haben, das die zu verschiebenden Ressourcen enthält.
    - Wenn Sie zum ersten Mal eine Ressource für ein bestimmtes Quelle-Ziel-Paar in einem Azure-Abonnement hinzufügen, erstellt Resource Mover eine [vom System zugewiesene verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (früher als Managed Service Identify (MSI) bezeichnet), die vom Abonnement als vertrauenswürdig eingestuft wird.
    - Zum Erstellen der Identität und zum Zuweisen der erforderlichen Rolle (Mitwirkender oder Benutzerzugriffsadministrator im Quellabonnement) benötigt das Konto, das Sie zum Hinzufügen von Ressourcen verwenden, Berechtigungen als *Besitzer* für das Abonnement. Hier [erfahren Sie mehr](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) zu Azure-Rollen.
- Das Abonnement benötigt ein ausreichendes Kontingent zum Erstellen der Ressourcen, die Sie in die Zielregion verschieben. Wenn kein ausreichendes Kontingent vorhanden ist, [fordern Sie eine Heraufsetzung des Kontingents an](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Überprüfen Sie die Preise und Gebühren für die Zielregion, in die Sie Ressourcen verschieben. Verwenden Sie hierfür den [Preisrechner](https://azure.microsoft.com/pricing/calculator/).
    

## <a name="check-sql-requirements"></a>SQL-Anforderungen überprüfen

1. [Überprüfen](support-matrix-move-region-sql.md) Sie, welche Funktionen der Datenbank/des Pools für elastische Datenbanken zum Verschieben in eine andere Region unterstützt werden.
2. Erstellen Sie in der Zielregion einen Zielserver für jeden Quellserver. [Weitere Informationen](../azure-sql/database/active-geo-replication-security-configure.md#how-to-configure-logins-and-users)
4. Wenn Datenbanken mit Transparent Data Encryption (TDE) verschlüsselt sind und Sie Ihren eigenen Verschlüsselungsschlüssel in Azure Key Vault verwenden, [erfahren Sie hier](../key-vault/general/move-region.md), wie Sie Schlüsseltresore in eine andere Region verschieben.
5. Wenn die SQL-Datensynchronisierung aktiviert ist, wird das Verschieben von Mitgliedsdatenbanken unterstützt. Nach der Verschiebung müssen Sie die SQL-Datensynchronisierung für die neue Zieldatenbank einrichten.
6. Entfernen Sie vor dem Verschieben die erweiterten Datensicherheitseinstellungen. Konfigurieren Sie nach dem Verschieben die [Einstellungen](../azure-sql/database/azure-defender-for-sql.md) auf SQL Server-Ebene in der Zielregion.
7. Wenn die Überwachung aktiviert ist, werden Richtlinien nach dem Verschieben auf den Standardwert zurückgesetzt. Richten Sie nach dem Verschieben die [Überwachung](../azure-sql/database/auditing-overview.md) erneut ein.
7. Sicherungsaufbewahrungsrichtlinien für die Quelldatenbank werden in die Zieldatenbank übertragen. [Erfahren Sie mehr](../azure-sql/database/long-term-backup-retention-configure.md) über das Ändern von Einstellungen nach dem Verschieben.
8. Entfernen Sie vor dem Verschieben Firewallregeln auf Serverebene. Firewallregeln auf Datenbankebene werden während des Verschiebens vom Quellserver auf den Zielserver kopiert. Richten Sie nach dem Verschieben [Firewallregeln](../azure-sql/database/firewall-create-server-level-portal-quickstart.md) für SQL Server in der Zielregion ein.
9. Entfernen Sie die Einstellungen für die automatische Optimierung vor dem Verschieben. [Richten Sie die automatische Optimierung](../azure-sql/database/automatic-tuning-enable.md) nach dem Verschieben wieder ein.
10. Entfernen Sie vor dem Verschieben die Einstellungen für die Datenbankwarnungen. Setzen Sie sie nach dem Verschieben wieder [zurück](../azure-sql/database/alerts-insights-configure-portal.md).
    
## <a name="select-resources"></a>Wählen Sie die Ressourcen aus

Wählen Sie die Ressourcen aus, die Sie verschieben möchten.

- Sie können einen beliebigen unterstützten Ressourcentyp in allen Ressourcengruppen in der ausgewählten Quellregion auswählen.
- Sie verschieben Ressourcen in eine Zielregion im gleichen Abonnement wie die Quellregion. Wenn Sie das Abonnement ändern möchten, können Sie dies nach dem Verschieben der Ressourcen tun.

1. Suchen Sie im Azure-Portal nach *Resource Mover*. Wählen Sie anschließend unter **Dienste** die Option **Azure Resource Mover** aus.

     ![Suchen nach Ergebnissen für Resource Mover im Azure-Portal](./media/tutorial-move-region-sql/search.png)

2. Klicken Sie in der **Übersicht** auf **Erste Schritte**.

    ![Schaltfläche zum Hinzufügen von Ressourcen zum Verschieben in eine andere Region](./media/tutorial-move-region-sql/get-started.png)

3. Wählen Sie unter **Ressourcen verschieben** > **Source + destination** (Quelle und Ziel) das Quellabonnement und die Region aus.
4. Wählen Sie unter **Ziel** die Region aus, in die Sie die Ressourcen verschieben möchten. Klicken Sie dann auf **Weiter**.

    ![Seite zum Auswählen der Quell- und Zielregion](./media/tutorial-move-region-sql/source-target.png)

6. Klicken Sie unter **Zu verschiebende Ressourcen** auf **Ressourcen auswählen**.
7. Wählen Sie unter **Ressourcen auswählen** die Ressourcen aus. Sie können nur Ressourcen hinzufügen, die zum Verschieben unterstützt werden. Klicken Sie anschließend auf **Fertig**.

    ![Seite zum Auswählen der zu verschiebenden SQL-Ressourcen](./media/tutorial-move-region-sql/select-resources.png)

8. Klicken Sie unter **Zu verschiebende Ressourcen** auf **Weiter**.

9. Überprüfen Sie unter **Review + Add** (Überprüfen und hinzufügen) die Quell- und Zieleinstellungen. Beachten Sie, dass die Metadaten zum Verschieben in einer Ressourcengruppe gespeichert werden, die zu diesem Zweck in der Metadatenregion erstellt wurde.


    ![Seite zum Überprüfen der Einstellungen und zum Fortsetzen der Verschiebung](./media/tutorial-move-region-sql/review.png)

10. Klicken Sie auf **Fortsetzen**, um die Ressourcen hinzuzufügen.
11. Nachdem der Vorgang zum Hinzufügen erfolgreich abgeschlossen wurde, klicken Sie auf **Adding resources for move** (Zu verschiebende Ressource werden hinzugefügt) im Benachrichtigungssymbol.
12. Nachdem Sie auf die Benachrichtigung geklickt haben, überprüfen Sie die Ressourcen auf der Seite **Across regions** (Regionsübergreifend).


> [!NOTE]
> 
> - SQL Server weist nun den Status *Manual assignment pending* (Manuelle Zuweisung ausstehend) auf.
> - Weitere hinzugefügte Ressourcen haben den Status *Prepare pending* (Vorbereitung ausstehend).
> - Wenn Sie eine Ressource aus einer Sammlung für die Verschiebung entfernen möchten, hängt die Methode davon ab, an welchem Punkt im Verschiebevorgang Sie sich befinden. [Weitere Informationen](remove-move-resources.md)

## <a name="resolve-dependencies"></a>Auflösen von Abhängigkeiten


1. Wenn unter **Across regions** (Regionsübergreifend) für Ressourcen in der Spalte **Probleme** die Meldung *Abhängigkeiten überprüfen* angezeigt wird, klicken Sie auf die Schaltfläche **Abhängigkeiten überprüfen**. Der Überprüfungsprozess wird daraufhin begonnen.
2. Wenn Abhängigkeiten gefunden werden, klicken Sie auf **Abhängigkeiten hinzufügen**.

    ![Schaltfläche zum Hinzufügen von Abhängigkeiten](./media/tutorial-move-region-sql/add-dependencies.png)
   
3. Wählen Sie unter **Abhängigkeiten hinzufügen** die abhängigen Ressourcen und **Abhängigkeiten hinzufügen** aus. Überwachen Sie den Fortschritt in den Benachrichtigungen.

4. Fügen Sie bei Bedarf zusätzliche Abhängigkeiten hinzu, und überprüfen Sie die Abhängigkeiten erneut. 

5. Überprüfen Sie auf der Seite **Across regions** (Regionsübergreifend), ob sich die Ressourcen jetzt im Status *Prepare pending* (Vorbereitung ausstehend) befinden und keine Probleme mehr aufweisen.

    ![Seite mit den Ressourcen im Status „Prepare pending“ (Vorbereitung ausstehend)](./media/tutorial-move-region-sql/prepare-pending.png)



## <a name="move-the-sql-server"></a>Verschieben von SQL Server

Weisen Sie der Zielregion einen SQL Server-Zielserver zu, und führen Sie einen Commit für die Verschiebung aus.

### <a name="assign-a-target-sql-server"></a>Zuweisen eines SQL Server-Zielservers

1. Klicken Sie unter **Across regions**(Regionsübergreifend) für die SQL Server-Ressource in der Spalte **Destination configuration** (Zielkonfiguration) auf **Resource not assigned** (Ressource nicht zugewiesen).
2. Wählen Sie eine vorhandene SQL Server-Ressource in der Zielregion aus. 
    
    ![Eintrag, der den Status von SQL Server als „Commit move pending“ (Committen der Verschiebung ausstehend) anzeigt](./media/tutorial-move-region-sql/sql-server-commit-move-pending.png) 

    
> [!NOTE]
> Der Status des SQL Server-Quellservers wird in *Commit move pending* (Committen der Verschiebung ausstehend) geändert. 

### <a name="commit-the-sql-server-move"></a>Committen der SQL Server-Verschiebung

1. Wählen Sie unter **Across regions** (Regionsübergreifend) SQL Server aus, und klicken Sie anschließend auf **Commit move** (Verschiebung committen).
2. Klicken Sie unter **Commit resources** (Ressourcen committen) auf **Commit**.

    ![Seite zum Committen der SQL Server-Verschiebung](./media/tutorial-move-region-sql/commit-sql-server.png)

3. Überwachen Sie den Fortschritt der Verschiebung in der Benachrichtigungsleiste.

> [!NOTE]
> Nach dem Commit befindet sich SQL Server im Status *Delete source pending* (Löschen der Quelle ausstehend).


## <a name="prepare-resources-to-move"></a>Vorbereiten der Ressource zum Verschieben

Nachdem der SQL Server-Quellserver verschoben wurde, können Sie die Verschiebung der anderen Ressourcen vorbereiten.

## <a name="prepare-an-elastic-pool"></a>Vorbereiten eines Pools für elastische Datenbanken

1. Wählen Sie unter **Across regions** den Quellpool für elastische Datenbanken („demo-test1-elasticpool“ in unserer exemplarischen Vorgehensweise) aus, und klicken Sie anschließend auf **Vorbereiten**.

    ![Schaltfläche zum Vorbereiten der Ressourcen](./media/tutorial-move-region-sql/prepare-elastic.png)

2. Klicken Sie unter **Ressourcen vorbereiten** auf **Vorbereiten**.
3. Wenn Benachrichtigungen anzeigen, dass der Vorbereitungsvorgang erfolgreich war, klicken Sie auf **Aktualisieren**.

> [!NOTE]
> Der Pool für elastische Datenbanken weist nun den Status *Initiate move pending* (Initiieren der Verschiebung ausstehend) auf.

## <a name="prepare-a-single-database"></a>Vorbereiten einer Einzeldatenbank

1. Wählen Sie unter **Across regions** (Regionsübergreifend) die Einzeldatenbank (nicht in einem Pool für elastische Datenbanken) aus, und klicken Sie anschließend auf **Vorbereiten**.

    ![Schaltfläche zum Vorbereiten der ausgewählten Ressourcen](./media/tutorial-move-region-sql/prepare-db.png)

2. Klicken Sie unter **Ressourcen vorbereiten** auf **Vorbereiten**.
3. Wenn Benachrichtigungen anzeigen, dass der Vorbereitungsvorgang erfolgreich war, klicken Sie auf **Aktualisieren**.

> [!NOTE]
> Die Datenbank weist jetzt den Status *Initiate move pending* (Initiieren der Verschiebung ausstehend) auf und wurde in der Zielregion erstellt.


## <a name="move-the-pool-and-prepare-pool-databases"></a>Verschieben des Pools und Vorbereiten von Pooldatenbanken

Zum Vorbereiten von Datenbanken in einem Pool für elastische Datenbanken muss der Pool für elastische Datenbanken den Status *Commit move pending* (Committen der Verschiebung ausstehend) aufweisen. Initiieren Sie die Verschiebung für den Pool, um zu diesem Status zu wechseln.

#### <a name="initiate-move---elastic-pool"></a>Initiieren der Verschiebung – Pool für elastische Datenbanken

1. Wählen Sie unter **Across regions** den Quellpool für elastische Datenbanken („demo-test1-elasticpool“ in unserer exemplarischen Vorgehensweise) aus, und klicken Sie anschließend auf **Initiate Move** (Verschiebung initiieren).
2. Klicken Sie unter **Ressourcen verschieben** auf **Initiate Move** (Verschiebung initiieren).

    
    ![Schaltfläche zum Initiieren der Verschiebung des Pools für elastische Datenbanken](./media/tutorial-move-region-sql/initiate-elastic.png)

1. Überwachen Sie den Fortschritt der Verschiebung in der Benachrichtigungsleiste.
1. Wenn Benachrichtigungen anzeigen, dass die Verschiebung erfolgreich war, klicken Sie auf **Aktualisieren**.

> [!NOTE]
> Der Pool für elastische Datenbanken weist nun den Status *Commit move pending* (Committen der Verschiebung ausstehend) auf.

#### <a name="prepare-database"></a>Vorbereiten einer Datenbank

1. Wählen Sie unter **Across regions** die Datenbank („demo-test2-sqldb“ in unserer exemplarischen Vorgehensweise) aus, und klicken Sie anschließend auf **Vorbereiten**.
2. Klicken Sie unter **Ressourcen vorbereiten** auf **Vorbereiten**.

    ![Schaltfläche zum Vorbereiten der Datenbank im Pool für elastische Datenbanken](./media/tutorial-move-region-sql/prepare-database-elastic.png) 

Während der Vorbereitung wird die Zieldatenbank in der Zielregion erstellt, und die Datenreplikation wird gestartet. Nach der Vorbereitung weist die Datenbank den Status *Initiate move pending* (Initiieren der Verschiebung ausstehend) auf. 

![Schaltfläche zum Vorbereiten der ausgewählten Datenbank im Pool für elastische Datenbanken](./media/tutorial-move-region-sql/initiate-move-pending.png) 

## <a name="move-databases"></a>Verschieben von Datenbanken

Beginnen Sie mit dem Verschieben der Datenbanken.
1. Wählen Sie unter **Across regions** (Regionsübergreifend) Ressourcen mit dem Status **Initiate move pending** (Initiieren der Verschiebung ausstehend) aus. Klicken Sie anschließend auf **Initiate move** (Verschiebung initiieren).
2. Klicken Sie unter **Ressourcen verschieben** auf **Initiate Move** (Verschiebung initiieren).

    ![Seite zum Initiieren der Verschiebung](./media/tutorial-move-region-sql/initiate-move.png)

3. Überwachen Sie den Fortschritt der Verschiebung in der Benachrichtigungsleiste.

> [!NOTE]
> Datenbanken weisen nun den Status *Commit move pending* (Committen der Verschiebung ausstehend) auf.


## <a name="discard-or-commit"></a>Verwerfen oder committen?

Nach der anfänglichen Verschiebung können Sie entscheiden, ob Sie einen Commit für die Verschiebung durchführen oder sie verwerfen möchten. 

- **Verwerfen**: Sie können eine Verschiebung verwerfen, wenn Sie Tests durchführen und die Quellressource letztendlich nicht verschoben werden soll. Beim Verwerfen der Verschiebung wird die Ressource in den Status **Initiate move pending** (Initiieren der Verschiebung ausstehend) zurückgesetzt.
- **Commit**: Beim Commit wird die Verschiebung in die Zielregion abgeschlossen. Nach dem Commit hat eine Quellressource den Status **Delete source pending** (Löschen der Quelle ausstehend), und Sie können entscheiden, ob sie gelöscht werden soll.


## <a name="discard-the-move"></a>Verwerfen der Verschiebung 

Sie können eine Verschiebung wie folgt verwerfen:

1. Wählen Sie unter **Across regions** (Regionsübergreifend) Ressourcen mit dem Status **Commit move pending** (Committen der Verschiebung ausstehend) aus, und klicken Sie auf **Discard move** (Verschiebung verwerfen).
2. Klicken Sie unter **Discard move** (Verschiebung verwerfen) auf **Verwerfen**.
3. Überwachen Sie den Fortschritt der Verschiebung in der Benachrichtigungsleiste.


> [!NOTE]
> - Nachdem Sie die Ressourcen verworfen haben, weisen diese den Status *Initiate move pending* (Initiieren der Verschiebung ausstehend) auf.
> - Wenn nur ein Pool für elastische Datenbanken vorhanden ist, verwerfen Sie den Verlauf, und der in der Zielregion erstellte Pool für elastische Datenbanken wird gelöscht.
> - Bei einem Pool für elastische Datenbanken mit zugeordneten Datenbanken im Status *Commit move pending* (Committen der Verschiebung ausstehend) können Sie den Pool für elastische Datenbanken nicht verwerfen.
> - Wenn Sie eine SQL-Datenbank verwerfen, werden die Ressourcen der Zielregion nicht gelöscht. 

Wenn Sie die Verschiebung nach dem Verwerfen erneut starten möchten, wählen Sie die SQL-Datenbank oder den Pool für elastische Datenbanken aus, und initiieren Sie die Verschiebung erneut.


## <a name="commit-the-move"></a>Commit der Verschiebung

Schließen Sie das Verschieben von Datenbanken und Pools für elastische Datenbanken wie folgt ab:


1. Überprüfen Sie, ob SQL Server den Status *Delete source pending* (Löschen der Quelle ausstehend) aufweist.
2. Aktualisieren Sie die Datenbank-Verbindungszeichenfolgen vor dem Commit in die Zielregion.
3. Wählen Sie unter **Across regions** (Regionsübergreifend) die SQL-Ressourcen aus, und klicken Sie anschließend auf **Commit move** (Verschiebung committen).
4. Klicken Sie unter **Commit resources** (Ressourcen committen) auf **Commit**.

    ![Committen der Verschiebung](./media/tutorial-move-region-sql/commit-sql-resources.png)

5. Verfolgen Sie den Fortschritt des Commits in der Benachrichtigungsleiste nach.


> [!NOTE]
> Bei SQL-Datenbanken kommt es während des Committens zu Downtime.
> Datenbanken und Pools für elastische Datenbanken, für die ein Commit ausgeführt wurde, weisen nun den Status *Delete source pending* (Löschen der Quelle ausstehend) auf.
> Aktualisieren Sie nach dem Commit die datenbankbezogenen Einstellungen, einschließlich der Firewallregeln, Richtlinien und Warnungen, in die Zieldatenbank.


## <a name="delete-source-resources-after-commit"></a>Löschen von Quellressourcen nach einem Commit

Nach dem Verschieben können Sie die Ressourcen in der Quellregion löschen, dies ist jedoch optional. 

1. Klicken Sie unter **Across regions** (Regionsübergreifend) auf den Namen der einzelnen Quellressourcen, die Sie löschen möchten.
2. Wählen Sie auf der Seite „Eigenschaften“ der ausgewählten Ressource die Option **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial führen Sie Folgendes durch:

> [!div class="checklist"]
> * Verschieben von Azure SQL-Datenbanken in eine andere Azure-Region
> * Verschieben von Azure SQL-Pools für elastische Datenbanken in eine andere Region

Versuchen Sie als Nächstes, Azure-VMs in eine andere Region zu verschieben.

> [!div class="nextstepaction"]
> [Verschieben von virtuellen Azure-Computern](./tutorial-move-region-virtual-machines.md)