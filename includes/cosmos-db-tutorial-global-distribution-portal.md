---
title: 'Azure Cosmos DB: globale Verteilung'
description: Hier erfahren Sie, wie Sie Daten mit Azure Cosmos DB im Azure-Portal global replizieren.
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 12/26/2018
ms.custom: include file
ms.openlocfilehash: 58788d6194454c8bd40730c9c350aa901924ba3d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027912"
---
## <a name="add-global-database-regions-using-the-azure-portal"></a><a id="addregion"></a>Hinzufügen globaler Datenbankregionen über das Azure-Portal
Azure Cosmos DB ist in allen [Azure-Regionen][azureregions] weltweit verfügbar. Nachdem Sie die Standardkonsistenzebene für Ihr Datenbankkonto ausgewählt haben, können Sie dem Konto eine oder mehrere Regionen zuordnen (je nachdem, welche Konsistenzebene Sie ausgewählt haben und welche Anforderungen an eine globale Verteilung bestehen).

1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) auf der linken Leiste auf **Azure Cosmos DB**.
2. Wählen Sie auf der Seite **Azure Cosmos DB** das zu ändernde Datenbankkonto aus.
3. Klicken Sie auf der Kontoseite im Menü auf **Daten global replizieren**.
4. Wählen Sie auf der Seite **Daten global replizieren** die Regionen aus, die Sie hinzufügen oder entfernen möchten. Klicken Sie hierzu auf die Regionen auf der Karte, und klicken Sie anschließend auf **Speichern**. Für das Hinzufügen von Regionen entstehen Kosten. Weitere Informationen hierzu finden Sie auf der Seite mit [Preisinformationen](https://azure.microsoft.com/pricing/details/cosmos-db/) sowie im Artikel [Globale Verteilung von Daten mit Azure Cosmos DB](../articles/cosmos-db/distribute-data-globally.md).
   
    ![Hinzufügen oder Entfernen von Regionen per Klick auf die Regionen auf der Karte][1]
    
Nachdem Sie eine zweite Region hinzugefügt haben, wird im Portal auf der Seite **Daten global replizieren** die Option **Manuelles Failover** aktiviert. Sie können diese Option verwenden, um den Failovervorgang, zu testen oder die primäre Schreibregion zu ändern. Nachdem Sie eine dritte Region hinzugefügt haben, wird auf der gleichen Seite die Option **Failoverprioritäten** aktiviert, sodass Sie die Failoverreihenfolge für Lesevorgänge ändern können.  

### <a name="selecting-global-database-regions"></a>Auswählen von globalen Datenbankregionen
Es gibt zwei gängige Szenarios zum Konfigurieren von mindestens zwei oder mehr Regionen:

1. Übermitteln von niedriger Latenz beim Zugriff auf Daten für Endbenutzer, unabhängig davon, wo sie sich befinden
2. Hinzufügen von regionaler Resilienz für Geschäftskontinuität und Notfallwiederherstellung (BCDR)

Zur Gewährleistung geringer Wartezeiten für Endbenutzer empfiehlt es sich, sowohl die Anwendung als auch Azure Cosmos DB in den Regionen bereitzustellen, in denen sich die Benutzer der Anwendung befinden.

Für BCDR empfiehlt es sich, die Regionen basierend auf den Regionspaaren hinzuzufügen, die im Artikel [Geschäftskontinuität und Notfallwiederherstellung: Azure-Regionspaare][bcdr] beschrieben werden.

<!--

## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your Cosmos DB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **Azure Cosmos DB** blade, select the database account to modify.
2. In the account blade, click **Replicate data globally** from the menu.
3. In the **Replicate data globally** blade, click **Manual Failover** from the top bar.
    ![Change the write region under Azure Cosmos DB Account > Replicate data globally > Manual Failover][2]
4. Select a read region to become the new write region, click the checkbox to confirm triggering a failover, and click OK
    ![Change the write region by selecting a new region in list under Azure Cosmos DB Account > Replicate data globally > Manual Failover][3]

--->

<!--Image references-->
[1]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-add-region.png
[2]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-1.png
[3]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: ../articles/best-practices-availability-paired-regions.md
[consistency]: ../articles/cosmos-db/consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/