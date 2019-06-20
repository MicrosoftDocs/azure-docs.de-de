---
title: Skalieren von Ressourcen für Pools für elastische Datenbanken – Azure SQL-Datenbank | Microsoft-Dokumentation
description: Diese Seite beschreibt die Skalierung von Ressourcen für Pools für elastische Datenbanken in Azure SQL-Datenbank.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 3/14/2019
ms.openlocfilehash: d8aaf51c836a8e88c4e9b92798067167cd044e72
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60848081"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Skalieren von Ressourcen für Pools für elastische Datenbanken in Azure SQL-Datenbank

In diesem Artikel wird beschrieben, wie die für Pools für elastische Datenbanken und Pooldatenbanken in Azure SQL-Datenbank verfügbaren Compute- und Speicherressourcen skaliert werden können.

## <a name="change-compute-resources-vcores-or-dtus"></a>Ändern der Computeressourcen (virtuelle Kerne oder DTUs)

Nach der anfänglichen Auswahl der Anzahl virtueller Kerne oder eDTUs können Sie einen Pool für elastische Datenbanken je nach tatsächlichem Bedarf über das [Azure-Portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), über [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), mithilfe der [Azure-Befehlszeilenschnittstelle](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update) oder über die [REST-API](https://docs.microsoft.com/rest/api/sql/elasticpools/update) dynamisch zentral hoch- oder herunterskalieren.

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Auswirkungen der Änderung der Dienstebene oder der Skalierung der Computegröße

Die Vorgehensweise zum Ändern der Dienstebene oder der Computegröße eines Pools für elastische Datenbanken ähnelt der Vorgehensweise für Einzeldatenbanken und umfasst im Wesentlichen folgende Schritte:

1. Erstellen einer neuen Computeinstanz für den Pool für elastische Datenbanken  

    Eine neue Computeinstanz für den Pool für elastische Datenbanken wird mit der angeforderten Dienstebene und Computegröße erstellt. Bei einigen Kombinationen von Dienstebenen- und Computegrößenänderungen muss in der neuen Computeinstanz jeweils ein Replikat der einzelnen Datenbanken erstellt werden. Dies umfasst das Kopieren von Daten und kann sich stark auf die Gesamtwartezeit auswirken. Die Datenbanken bleiben unabhängig davon während dieses Schritts online, und Verbindungen werden weiterhin an die Datenbanken in der ursprünglichen Computeinstanz weitergeleitet.

2. Umleiten der Verbindungen zur neuen Computeinstanz

    Vorhandene Verbindungen mit den Datenbanken in der ursprünglichen Computeinstanz werden verworfen. Alle neuen Verbindungen werden mit den Datenbanken in der neuen Computeinstanz hergestellt. Bei einigen Kombinationen von Dienstebenen- und Computegrößenänderungen werden Datenbankdateien während des Wechsels getrennt und neu angefügt.  Die Umstellung kann zu einer kurzen Dienstunterbrechung führen, in der Datenbanken in der Regel für weniger als 30 Sekunden nicht verfügbar ist. Sollten zu dem Zeitpunkt, zu dem die Verbindungen getrennt werden, zeitintensive Transaktionen ausgeführt werden, kann dieser Schritt länger dauern, da abgebrochene Transaktionen wiederhergestellt werden müssen. Mit der [schnelleren Datenbankwiederherstellung](sql-database-accelerated-database-recovery.md) können die Auswirkungen abgebrochener, zeitintensiver Transaktionen reduziert werden.

> [!IMPORTANT]
> Während dieses Workflows gehen keine Daten verloren.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Wartezeit beim Ändern der Dienstebene oder beim Skalieren der Computegröße

Die Wartezeit beim Ändern der Dienstebene oder beim Skalieren der Computegröße einer Einzeldatenbank oder eines Pools für elastische Datenbanken wird wie folgt parametrisiert:

|Dienstebene|Einzeldatenbank des Tarifs „Basic“,</br>Standard (S0-S1)|Pools für elastische Datenbanken des Tarifs „Basic“,</br>Standard (S2-S12), </br>Hyperscale, </br>Einzeldatenbank oder Pool für elastische Datenbanken des Tarifs „Universell“|Einzeldatenbank oder Pool für elastische Datenbanken der Tarife „Premium“ oder „Unternehmenskritisch“|
|:---|:---|:---|:---|
|**Einzeldatenbank des Tarifs „Basic“,</br> Standard (S0-S1)**|&bull; &nbsp;Konstante Wartezeit unabhängig vom verwendeten Speicherplatz</br>&bull; &nbsp;In der Regel weniger als fünf Minuten|&bull; &nbsp;Wartezeit proportional zum verwendeten Datenbankspeicherplatz aufgrund des Kopierens von Daten</br>&bull; &nbsp;In der Regel weniger als eine Minute pro GB (verwendeter Speicherplatz)|&bull; &nbsp;Wartezeit proportional zum verwendeten Datenbankspeicherplatz aufgrund des Kopierens von Daten</br>&bull; &nbsp;In der Regel weniger als eine Minute pro GB (verwendeter Speicherplatz)|
|**Pool für elastische Datenbanken des Tarifs „Basic“, </br>Standard (S2-S12), </br>Hyperscale, </br>Einzeldatenbank oder Pool für elastische Datenbanken des Tarifs „Universell“**|&bull; &nbsp;Wartezeit proportional zum verwendeten Datenbankspeicherplatz aufgrund des Kopierens von Daten</br>&bull; &nbsp;In der Regel weniger als eine Minute pro GB (verwendeter Speicherplatz)|&bull; &nbsp;Konstante Wartezeit unabhängig vom verwendeten Speicherplatz</br>&bull; &nbsp;In der Regel weniger als fünf Minuten|&bull; &nbsp;Wartezeit proportional zum verwendeten Datenbankspeicherplatz aufgrund des Kopierens von Daten</br>&bull; &nbsp;In der Regel weniger als eine Minute pro GB (verwendeter Speicherplatz)|
|**Einzeldatenbank oder Pool für elastische Datenbanken der Tarife „Premium“ oder „Unternehmenskritisch“**|&bull; &nbsp;Wartezeit proportional zum verwendeten Datenbankspeicherplatz aufgrund des Kopierens von Daten</br>&bull; &nbsp;In der Regel weniger als eine Minute pro GB (verwendeter Speicherplatz)|&bull; &nbsp;Wartezeit proportional zum verwendeten Datenbankspeicherplatz aufgrund des Kopierens von Daten</br>&bull; &nbsp;In der Regel weniger als eine Minute pro GB (verwendeter Speicherplatz)|&bull; &nbsp;Wartezeit proportional zum verwendeten Datenbankspeicherplatz aufgrund des Kopierens von Daten</br>&bull; &nbsp;In der Regel weniger als eine Minute pro GB (verwendeter Speicherplatz)|

> [!NOTE]
>
> - Beim Ändern der Dienstebene oder Skalieren der Computegröße für einen Pool für elastische Datenbanken muss der verwendete Speicherplatz aller Datenbanken im Pool addiert werden, um die Schätzung zu berechnen.
> - Wenn eine Datenbank in einen oder aus einem Pool für elastische Datenbanken verschoben wird, ist für die Wartezeit nur der Speicherplatz relevant, der von der Datenbank verwendet wird, nicht der verwendete Speicherplatz des Pools für elastische Datenbanken.
>
> [!TIP]
> Weitere Informationen zum Überwachen aktuell ausgeführter Vorgänge finden Sie unter: [Verwalten von Vorgängen mit der SQL-REST-API](https://docs.microsoft.com/rest/api/sql/operations/list), [Verwalten von Vorgängen mithilfe der CLI](/cli/azure/sql/db/op), [Überwachen von Vorgängen mit T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) und unter diesen beiden PowerShell-Befehlen: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) und [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Weitere Überlegungen zum Ändern der Dienstebene oder Skalieren der Computegröße

- Beim Verkleinern von virtuellen Kernen oder eDTUs für einen Pool für elastische Datenbanken muss die verwendete Poolspeichermenge kleiner als die maximal zulässige Größe der gewünschten Dienstebene und Pool-eDTUs sein.
- Beim Skalieren von virtuellen Kernen oder eDTUs für einen Pool für elastische Datenbanken fallen zusätzliche Speicherkosten an, wenn (1) die maximale Speichergröße des Pools vom Zielpool unterstützt wird und (2) die maximale Speichergröße die enthaltene Speichermenge für den Zielpool überschreitet. Wenn z.B. ein 100-eDTU-Standard-Pool mit einer maximalen Größe von 100 GB zu einem 50-eDTU-Standard-Pool herabgestuft wird, fallen zusätzliche Speicherkosten an, da der Zielpool eine maximale Größe von 100 GB unterstützt, und die enthaltene Speichermenge nur 50 GB beträgt. Daher beträgt die zusätzliche Speichermenge 100 GB – 50 GB = 50 GB. Ausführliche Informationen zu den Preisen für zusätzlichen Speicherplatz siehe [SQL-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-database/). Wenn die tatsächlich verwendete Speichermenge kleiner als die enthaltene Speichermenge ist, können Sie diese zusätzlichen Kosten vermeiden, indem Sie die maximale Datenbankgröße auf die enthaltene Menge reduzieren.

### <a name="billing-during-rescaling"></a>Abrechnung während der Skalierung

Die Abrechnung erfolgt für jede Stunde, in der eine Datenbank die höchste in dieser Stunde angewendete Dienstebene und Computegröße nutzt – unabhängig von der Verwendung der Datenbank und ob sie weniger als eine Stunde aktiv war. Wenn Sie beispielsweise eine Einzeldatenbank erstellen und diese fünf Minuten später löschen, wird Ihnen eine volle Datenbankstunde in Rechnung gestellt.

## <a name="change-elastic-pool-storage-size"></a>Ändern der Speichergröße in einem Pool für elastische Datenbanken

> [!IMPORTANT]
> Unter bestimmten Umständen müssen Sie ggf. eine Datenbank verkleinern, um ungenutzten Speicherplatz freizugeben. Weitere Informationen finden Sie unter [Verwalten von Dateispeicherplatz in Azure SQL-Datenbank](sql-database-file-space-management.md).

### <a name="vcore-based-purchasing-model"></a>vCore-basiertes Kaufmodell

- Speicher kann bis zur maximalen Speichergröße bereitgestellt werden:

  - In den Dienstebenen „Standard“ oder „Universell“ erhöhen oder verringern Sie die Speichergröße in Schritten von 10 GB.
  - In den Dienstebenen „Premium“ oder „Unternehmenskritisch“ erhöhen oder verringern Sie die Speichergröße in Schritten von 250 GB
- Der Speicher für einen Pool für elastische Datenbanken kann durch Erhöhen oder Verringern der Maximalgröße bereitgestellt werden.
- Der Preis für den Speicher für einen Pool für elastische Datenbanken errechnet sich aus der Menge an bereitgestelltem Speicher multipliziert mit dem Speichereinheitenpreis für die Dienstebene. Ausführliche Informationen zu den Preisen für zusätzlichen Speicherplatz siehe [SQL-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Unter bestimmten Umständen müssen Sie ggf. eine Datenbank verkleinern, um ungenutzten Speicherplatz freizugeben. Weitere Informationen finden Sie unter [Verwalten von Dateispeicherplatz in Azure SQL-Datenbank](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>DTU-basiertes Kaufmodell

- Der eDTU-Preis für einen Pool für elastische Datenbanken enthält eine bestimmte Menge Speicher ohne zusätzliche Kosten. Zusätzlicher Speicher über die inbegriffene Speichermenge hinaus kann gegen zusätzliche Gebühren bis zur Obergrenze in Inkrementen von 250 GB bis zu 1 TB und dann in Inkrementen von 256 GB über 1 TB hinaus bereitgestellt werden. Informationen zu enthaltenen Speichermengen und Maximalgrößen finden Sie unter [Pool für elastische Datenbanken: Speicher- und Computegrößen](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
- Zusätzlicher Speicher für einen Pool für elastische Datenbanken kann durch Erhöhen der maximalen Größe mithilfe von [Azure-Portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), [Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update) oder [REST-API](https://docs.microsoft.com/rest/api/sql/elasticpools/update) bereitgestellt werden.
- Der Preis für zusätzlichen Speicher für einen Pool für elastische Datenbanken errechnet sich aus der Menge an zusätzlich bereitgestelltem Speicher multipliziert mit dem Einheitenpreis für zusätzlichen Speicher für die Dienstebene. Ausführliche Informationen zu den Preisen für zusätzlichen Speicherplatz siehe [SQL-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Unter bestimmten Umständen müssen Sie ggf. eine Datenbank verkleinern, um ungenutzten Speicherplatz freizugeben. Weitere Informationen finden Sie unter [Verwalten von Dateispeicherplatz in Azure SQL-Datenbank](sql-database-file-space-management.md).

## <a name="next-steps"></a>Nächste Schritte

Allgemeine Ressourcenlimits finden Sie unter [Ressourcenlimits des auf virtuellen Kernen basierenden Kaufmodells in SQL-Datenbank – Pools für elastische Datenbanken](sql-database-vcore-resource-limits-elastic-pools.md) und unter [Ressourcenlimits des auf DTUs basierenden Kaufmodells in SQL-Datenbank – Pools für elastische Datenbanken](sql-database-dtu-resource-limits-elastic-pools.md).
