---
title: 'Schnellstart: Konfigurieren der Workloadisolation – Portal'
description: Verwenden Sie das Azure-Portal zum Konfigurieren der Workloadisolation für einen dedizierten SQL-Pool.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 05/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 302249b7d8490e43b841116c52500e686626433d
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460626"
---
# <a name="quickstart-configure-dedicated-sql-pool-workload-isolation-using-a-workload-group-in-the-azure-portal"></a>Schnellstart: Konfigurieren der Workloadisolation eines dedizierten SQL-Pools mithilfe einer Arbeitsauslastungsgruppe im Azure-Portal

In dieser Schnellstartanleitung konfigurieren Sie die [Workloadisolation](sql-data-warehouse-workload-isolation.md), indem Sie eine Arbeitsauslastungsgruppe für das Reservieren von Ressourcen erstellen.  Für dieses Tutorial erstellen Sie die Arbeitsauslastungsgruppe `DataLoads` zum Laden von Daten. Die Arbeitsauslastungsgruppe reserviert 20 Prozent der Systemressourcen.  Bei einer Isolation von 20 Prozent für Datenladevorgänge sind Ressourcen garantiert, die die Einhaltung von SLAs ermöglichen.  Nach der Erstellung der Arbeitsauslastungsgruppe [erstellen Sie einen Workloadklassifizierer](quickstart-create-a-workload-classifier-portal.md), um dieser Arbeitsauslastungsgruppe Abfragen hinzuzufügen.


Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.


## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

> [!NOTE]
> Das Erstellen einer Instanz des dedizierten SQL-Pools in Azure Synapse Analytics führt möglicherweise zu einem neuen abrechenbaren Dienst.  Weitere Informationen finden Sie unter [Azure Synapse Analytics – Preise](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Voraussetzungen

In dieser Schnellstartanleitung wird vorausgesetzt, dass Sie bereits über eine Instanz des dedizierten SQL-Pools in Synapse SQL und über CONTROL DATABASE-Berechtigungen verfügen. Wenn Sie eine Instanz erstellen müssen, erstellen Sie anhand der Informationen unter [Schnellstart: Erstellen eines dedizierten SQL-Pools (Vorschau) über das Azure-Portal](../quickstart-create-sql-pool-portal.md) ein Data Warehouse mit dem Namen **mySampleDataWarehouse**.

>[!IMPORTANT] 
>Der dedizierte SQL-Pool muss online sein, damit die Workloadverwaltung konfiguriert werden kann. 

## <a name="configure-workload-isolation"></a>Konfigurieren der Workloadisolation

Durch die Erstellung von Arbeitsauslastungsgruppen können dedizierte SQL-Poolressourcen isoliert und für bestimmte Workloads reserviert werden.  Ausführlichere Informationen dazu, wie Sie mithilfe von Arbeitsauslastungsgruppen Workloads verwalten können, finden Sie in der Dokumentation zur [Workloadisolation](sql-data-warehouse-workload-isolation.md)  Anhand der Informationen unter [Schnellstart: Erstellen und Abfragen eines Synapse-SQL-Pools mit dem Azure-Portal](create-data-warehouse-portal.md) wurde **mySampleDataWarehouse** erstellt und mit DW100c initialisiert. Mit den folgenden Schritten erstellen Sie eine Arbeitsauslastungsgruppe in **mySampleDataWarehouse**.

So erstellen Sie eine Arbeitsauslastungsgruppe mit einer Isolation von 20 Prozent:
1.  Navigieren Sie zur Seite des dedizierten SQL-Pools **mySampleDataWarehouse**.
1.  Wählen Sie **Workloadverwaltung** aus.
1.  Wählen Sie **Neue Arbeitsauslastungsgruppe** aus.
1.  Wählen Sie **Benutzerdefiniert** aus.

    ![Klicken auf „Benutzerdefiniert“](./media/quickstart-configure-workload-isolation-portal/create-wg.png)

6.  Geben Sie `DataLoads` unter **Arbeitsauslastungsgruppe** ein.
7.  Geben Sie `20` unter **Prozentuale Mindestmenge für Ressourcen** ein.
8.  Geben Sie `5` unter **Prozentuale Mindestmenge für Ressourcen pro Anforderung** ein.
9.  Geben Sie `100` unter **Ressourcenbegrenzung (%)** ein.
10. Wählen Sie **Speichern** aus.

   ![Klicken Sie auf Speichern.](./media/quickstart-configure-workload-isolation-portal/configure-wg.png)

Eine Portalbenachrichtigung wird angezeigt, wenn die Arbeitsauslastungsgruppe erstellt wird.  Die Ressourcen der Arbeitsauslastungsgruppe werden in den Diagrammen unterhalb der konfigurierten Werte angezeigt.

   ![Klicken auf „Endgültig“](./media/quickstart-configure-workload-isolation-portal/display-wg.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

So löschen Sie die in diesem Tutorial erstellte Arbeitsauslastungsgruppe `DataLoads`:
1. Klicken Sie rechts neben der Arbeitsauslastungsgruppe `DataLoads` auf die Schaltfläche **`...`** .
2. Klicken Sie auf **Arbeitsauslastungsgruppe löschen**.
3. Wenn Sie aufgefordert werden, das Löschen der Arbeitsauslastungsgruppe zu bestätigen, klicken Sie auf **Ja**.
4. Klicken Sie auf **Speichern**.

   ![Klicken auf „Löschen“](./media/quickstart-configure-workload-isolation-portal/delete-wg.png)



Ihnen werden Gebühren für Ihre Data Warehouse-Einheiten und die in Ihrem Data Warehouse gespeicherten Daten berechnet. Diese Compute- und Speicherressourcen werden separat in Rechnung gestellt.

- Falls Sie die Daten im Speicher belassen möchten, können Sie Computeressourcen anhalten, wenn Sie das Data Warehouse nicht verwenden. Wenn Sie Computeressourcen anhalten, werden Ihnen nur die Datenspeichergebühren in Rechnung gestellt. Sie können die Computeressourcen fortsetzen, wenn Sie mit den Daten arbeiten möchten.
- Wenn zukünftig keine Gebühren anfallen sollen, können Sie das Data Warehouse löschen.

Führen Sie die folgenden Schritte aus, um Ressourcen zu bereinigen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie Ihren dedizierten SQL-Pool aus.

    ![Bereinigen von Ressourcen](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Wählen Sie zum Anhalten von Computeressourcen die Schaltfläche **Anhalten**. Wenn das Data Warehouse angehalten ist, wird die Schaltfläche **Starten** angezeigt.  Wählen Sie zum Fortsetzen der Computeressourcen die Option **Starten**.

3. Wählen Sie **Löschen**, wenn Sie die Data Warehouse-Instanz entfernen möchten, damit keine Gebühren für Compute- oder Speicherressourcen anfallen.

## <a name="next-steps"></a>Nächste Schritte

Zur Verwendung der Arbeitsauslastungsgruppe `DataLoads` muss ein [Workloadklassifizierer](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) erstellt werden, um Anforderungen an die Arbeitsauslastungsgruppe weiterzuleiten.  Fahren Sie mit dem Tutorial zum [Erstellen eines Workloadklassifizierers](quickstart-create-a-workload-classifier-portal.md) fort, um einen Workloadklassifizierer für `DataLoads` zu erstellen.

## <a name="see-also"></a>Weitere Informationen
Ausführliche Informationen zum Überwachen von Workloads für die Workloadverwaltung finden Sie im Artikel [Verwalten und Überwachen der Workloadpriorität in Azure Synapse Analytics](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
