---
title: Übersicht über das Kaufmodell für virtuelle Kerne
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Mit dem Kaufmodell für virtuelle Kerne können Sie Compute- und Speicherressourcen einzeln skalieren, eine Leistung wie in Ihrer lokalen Umgebung erzielen und den Preis für Azure SQL-Datenbank und Azure SQL Managed Instance optimieren.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 09/30/2020
ms.openlocfilehash: b4473ea304176615c35205494f342922869b71ea
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793142"
---
# <a name="vcore-model-overview---azure-sql-database-and-azure-sql-managed-instance"></a>Übersicht über das Modell für virtuelle Kerne – Azure SQL-Datenbank und Azure SQL Managed Instance 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Das Kaufmodell für virtuelle Kerne von Azure SQL-Datenbank und Azure SQL Managed Instance bietet einige Vorteile:

- Höhere Grenzwerte für Compute, Arbeitsspeicher, E/A und Speicher
- Kontrolle über die Hardwaregeneration, um besser auf Compute- und Arbeitsspeicheranforderungen der Workload reagieren zu können
- Preisrabatte für [Azure-Hybridvorteil](../azure-hybrid-benefit.md) und [reservierte Instanzen (RI)](reserved-capacity-overview.md)
- Mehr Transparenz bei den Hardwaredetails für das Computing zur besseren Planung von Migrationsvorgängen von lokalen Bereitstellungen

## <a name="service-tiers"></a>Dienstebenen

Als Optionen für die Dienstebene stehen im V-Kern-Modell „Universell“, „Unternehmenskritisch“ und „Hyperscale“ zur Verfügung. Die Dienstebene definiert ganz allgemein die Speicherarchitektur, die Speicherplatz- und E/A-Grenzwerte sowie Optionen für die Geschäftskontinuität im Zusammenhang mit der Verfügbarkeit und Notfallwiederherstellung.

|-|**Allgemeiner Zweck**|**Unternehmenskritisch**|**Hyperscale**|
|---|---|---|---|
|Am besten geeignet für:|Die meisten geschäftlichen Workloads. Bietet budgetorientierte, ausgewogene und skalierbare Compute- und Speicheroptionen. |Bietet Geschäftsanwendungen die höchste Resilienz gegenüber Fehlern durch die Verwendung mehrerer isolierter Replikate sowie die höchste E/A-Leistung pro Datenbankreplikat.|Die meisten geschäftlichen Workloads mit hohen Anforderungen an skalierbaren Speicher und Leseskalierung.  Bietet eine höhere Ausfallsicherheit, da mehrere isolierte Datenbankreplikate konfiguriert werden können. |
|Storage|Verwendet Remotespeicher.<br/>**Bereitgestelltes SQL-Datenbank-Computing:**<br/>5 GB – 4 TB<br/>**Serverloses Computing** :<br/>5 GB bis 3 TB<br/>**SQL Managed Instance:** 32 GB – 8 TB |Verwendet lokalen SSD-Speicher.<br/>**Bereitgestelltes SQL-Datenbank-Computing:**<br/>5 GB – 4 TB<br/>**SQL Managed Instance:**<br/>32 GB – 4 TB |Flexible automatische Speichervergrößerung nach Bedarf. Unterstützt bis zu 100 TB Speicher. Verwendet lokalen SSD-Speicher für den lokalen Pufferpoolcache und den lokalen Datenspeicher. Verwendet Azure-Remotespeicher als endgültigen langfristigen Datenspeicher. |
|IOPS und Durchsatz (ungefähr)|**SQL-Datenbank:** Weitere Informationen finden Sie in den Ressourcenlimits für [Einzeldatenbanken](resource-limits-vcore-single-databases.md) und [Pools für elastische Datenbanken](resource-limits-vcore-elastic-pools.md).<br/>**SQL Managed Instance:** Weitere Informationen finden Sie in der [Übersicht über die Ressourcenlimits von Azure SQL Managed Instance](../managed-instance/resource-limits.md#service-tier-characteristics).|Weitere Informationen finden Sie in den Ressourcenlimits für [Einzeldatenbanken](resource-limits-vcore-single-databases.md) und [Pools für elastische Datenbanken](resource-limits-vcore-elastic-pools.md).|Hyperscale ist eine mehrstufige Architektur mit Caching auf mehreren Ebenen. Die tatsächlichen Werte für IOPS und Durchsatz hängen vom Workload ab.|
|Verfügbarkeit|Einzelnes Replikat, keine Replikate mit Leseskalierung|3 Replikate, 1 [Replikat, Leseskalierung](read-scale-out.md),<br/>zonenredundante Hochverfügbarkeit (High Availability, HA)|Einzelnes Replikat mit Lese-/Schreibzugriff sowie bis zu vier [Replikate mit Leseskalierung](read-scale-out.md)|
|Backups|[Georedundanter Speicher mit Lesezugriff (RA-GRS)](../../storage/common/geo-redundant-design.md), sieben bis 35 Tage (standardmäßig sieben Tage)|[RA-GRS](../..//storage/common/geo-redundant-design.md), 7-35 Tage (standardmäßig 7 Tage)|Auf Momentaufnahmen basierende Sicherungen in Azure-Remotespeicher. Bei Wiederherstellungen werden diese Momentaufnahmen zur schnellen Wiederherstellung verwendet. Sicherungen werden sofort ausgeführt und haben keine Auswirkungen auf die E/A-Computeleistung. Wiederherstellungen sind schnell und nicht datenintensiv (dauern also nicht Stunden oder Tage, sondern nur Minuten).|
|In-Memory|Nicht unterstützt|Unterstützt|Nicht unterstützt|
|||


### <a name="choosing-a-service-tier"></a>Auswählen einer Dienstebene

Weitere Informationen zum Auswählen einer Dienstebene für die jeweilige Workload finden Sie in den folgenden Artikeln:

- [Wann sollte die Dienstebene „Universell“ gewählt werden?](service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Wann sollte die Dienstebene „Unternehmenskritisch“ ausgewählt werden?](service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Wann sollte die Dienstebene „Hyperscale“ ausgewählt werden?](service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Computetarife

Als Optionen für den Computetarif stehen im V-Kern-Modell die bereitgestellten und serverlosen Computetarife zur Auswahl.


### <a name="provisioned-compute"></a>Bereitgestelltes Computing

Bereitgestellte Computetarife bieten eine bestimmte Menge an Computeressourcen, die unabhängig von der Workloadaktivität ständig bereitgestellt werden. Die Abrechnung erfolgt dann zu einem Festpreis pro Stunde für die Menge an bereitgestellten Computeressourcen.


### <a name="serverless-compute"></a>Serverloses Computing

Beim [serverlosen Computetarif](serverless-tier-overview.md) erfolgt auch eine automatische Skalierung der Computeressourcen basierend auf den Workloadaktivitäten, und die Abrechnung erfolgt nach Menge der verbrauchten Computeressourcen pro Sekunde.



## <a name="hardware-generations"></a>Hardwaregenerationen

Zu den Optionen für die Hardwaregeneration im V-Kern-Modell gehören Gen4/Gen5, M-Serie und Fsv2-Serie. Die Hardwaregeneration definiert im Allgemeinen die Compute- und Arbeitsspeicherlimits sowie weitere Eigenschaften, die sich auf die Leistung der Workload auswirken.

### <a name="gen4gen5"></a>Gen4/Gen5

- Gen4/Gen5-Hardware bietet ausgeglichene Compute- und Arbeitsspeicherressourcen und eignet sich für die meisten Datenbankworkloads, die keine erhöhten Anforderungen an Arbeitsspeicher, virtuelle Kerne oder schnellere Einzel-V-Kerne stellen, wie sie von der Fsv2-Serie oder der M-Serie bereitgestellt werden.

Informationen zu den Regionen, in denen Gen4/Gen5 verfügbar ist, finden Sie unter [Verfügbarkeit von Gen4/Gen5](#gen4gen5-1).

### <a name="fsv2-series"></a>Fsv2-Serie

- Die Fsv2-Serie ist eine für Compute optimierte Hardwareoption, die eine niedrige CPU-Latenz und eine hohe Taktfrequenz für die meisten Workloads mit hohen CPU-Anforderungen bereitstellt.
- Abhängig von der Workload kann die Fsv2-Serie eine höhere CPU-Leistung pro virtuellem Kern erzielen als Gen5, und mit der Größe von 72 V-Kernen erzielen Sie mehr CPU-Leistung für weniger Kosten als mit 80 virtuellen Kernen bei Gen5. 
- Fsv2 bietet weniger Arbeitsspeicher und tempdb pro virtuellem Kern als andere Hardware, sodass für Workloads, für die diese Grenzwerte wichtig sind, stattdessen Gen5 oder die M-Serie in Erwägung gezogen werden sollte.  

Fsv2-series wird von der Dienstebene „Universell“ nicht unterstützt. Weitere Informationen zu Regionen, in denen die Fsv2-Serie verfügbar ist, finden Sie unter [Verfügbarkeit der Fsv2-Serie](#fsv2-series-1).


### <a name="m-series"></a>M-Serie

- Die M-Serie ist eine arbeitsspeicheroptimierte Hardwareoption für Workloads, die mehr Arbeitsspeicher und höhere Computegrenzwerte erfordert, als von Gen5 bereitgestellt werden.
- Die M-Serie bietet 29 GB pro V-Kern und bis zu 128 V-Kerne. Dadurch erhöht sich der Grenzwert für den Arbeitsspeicher im Vergleich zu Gen5 um das Achtfache auf fast 4 TB.

Die M-Serie wird nur auf der Dienstebene „Unternehmenskritisch“ unterstützt und unterstützt keine Zonenredundanz.  Weitere Informationen zu Regionen, in denen die M-Serie verfügbar ist, finden Sie unter [Verfügbarkeit der M-Serie](#m-series-1).

#### <a name="azure-offer-types-supported-by-m-series"></a>Von der M-Serie unterstützte Azure-Angebotstypen

Für den Zugriff auf die M-Serie muss das Abonnement ein kostenpflichtiger Angebotstyp sein, einschließlich nutzungsbasierter Bezahlung oder Enterprise Agreement (EA).  Eine vollständige Liste mit den von der M-Serie unterstützten Azure-Angebotstypen finden Sie unter [Aktuelle Angebote ohne Ausgabenlimits](https://azure.microsoft.com/support/legal/offer-details).

<!--
To enable M-series hardware for a subscription and region, a support request must be opened. The subscription must be a paid offer type including Pay-As-You-Go or Enterprise Agreement (EA).  If the support request is approved, then the selection and provisioning experience of M-series follows the same pattern as for other hardware generations. For regions where M-series is available, see [M-series availability](#m-series).
-->

### <a name="compute-and-memory-specifications"></a>Spezifikationen zu Compute- und Arbeitsspeicherressourcen


|Hardwaregeneration  |Compute  |Arbeitsspeicher  |
|:---------|:---------|:---------|
|Gen4     |– Intel E5-2673 v3-Prozessoren (Haswell) mit 2,4 GHz<br>- Bereitstellung von bis zu 24 virtuellen Kernen (1 virtueller Kern = 1 physischer Kern)  |- 7 GB pro virtuellem Kern<br>- Bereitstellung von bis zu 168 GB|
|Gen5     |**Bereitgestelltes Computing**<br>– Intel® E5-2673 v4-Prozessoren (Broadwell) mit 2,3 GHz, Intel® SP-8160-Prozessoren (Skylake)\* und Intel® 8272CL-Prozessoren (Cascade Lake) mit 2,5 GHz\*<br>- Bereitstellung von bis zu 80 virtuellen Kernen (1 virtueller Kern = 1 Hyperthread)<br><br>**Serverloses Computing** :<br>– Intel E5-2673 v4-Prozessoren (Broadwell) und Intel SP-8160-Prozessoren (Skylake)* mit 2,3 GHz<br>– Automatisches Hochskalieren auf bis zu 40 virtuelle Kerne (1 virtueller Kern = 1 Hyperthread)|**Bereitgestelltes Computing**<br>- 5,1 GB pro virtuellem Kern<br>- Bereitstellung von bis zu 408 GB<br><br>**Serverloses Computing** :<br>- Automatische Skalierung auf bis zu 24 GB pro V-Kern<br>– Automatisches Hochskalieren auf bis zu 120 GB|
|Fsv2-Serie     |– Intel® 8168-Prozessoren (Skylake)<br>- Kontinuierliche Turbo-Taktfrequenz von 3,4 GHz für alle Kerne und maximale Turbo-Taktfrequenz für Einzelkerne von 3,7 GHz<br>– Bereitstellung von bis zu 72 virtuellen Kernen (1 virtueller Kern = 1 Hyperthread)|- 1,9 GB pro virtuellem Kern<br>– Bereitstellung von bis zu 136 GB|
|M-Serie     |– Intel® E7-8890 v3-Prozessoren mit 2,5 GHz und Intel® 8280M-Prozessoren mit 2,7 GHz (Cascade Lake)<br>– Bereitstellung von bis zu 128 virtuellen Kernen (1 virtueller Kern = 1 Hyperthread)|- 29 GB pro virtuellem Kern<br>– Bereitstellung von bis zu 3,7 GB|

\* In der dynamischen Verwaltungssicht [sys. dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) wird die Hardwaregenerierung für Datenbanken mit Intel® SP-8160-Prozessoren (Skylake) als „Gen6“ und die für Datenbanken mit Intel® 8272CL-Prozessoren (Cascade Lake) als „Gen7“ angezeigt. Ressourcenlimits für alle Gen5-Datenbanken sind unabhängig vom Prozessortyp (Broadwell, Skylake oder Cascade Lake) identisch.

Weitere Informationen zu Ressourcenlimits finden Sie unter [Ressourcenlimits für Singletons (V-Kern)](resource-limits-vcore-single-databases.md) oder [Ressourcenlimits für Pools für elastische Datenbanken (V-Kern)](resource-limits-vcore-elastic-pools.md).

### <a name="selecting-a-hardware-generation"></a>Auswählen einer Hardwaregeneration

Im Azure-Portal können Sie die Hardwaregeneration für eine Datenbank oder einen Pool in SQL-Datenbank zum Zeitpunkt der Erstellung auswählen. Sie können auch die Hardwaregeneration einer vorhandenen Datenbank oder eines Pools ändern.

**So wählen Sie eine Hardwaregeneration beim Erstellen einer SQL-Datenbank oder eines Pools aus**

Ausführliche Informationen finden Sie unter [Erstellen einer SQL-Datenbank-Instanz](single-database-create-quickstart.md).

Wählen Sie auf der Registerkarte **Grundlagen** im Abschnitt **Compute + Speicher** den Link **Datenbank konfigurieren** und dann den Link **Konfiguration ändern** aus:

  ![Datenbank konfigurieren](./media/service-tiers-vcore/configure-sql-database.png)

Wählen Sie die gewünschte Hardwaregeneration aus:

  ![Auswählen der Hardware](./media/service-tiers-vcore/select-hardware.png)


**So ändern Sie die Hardwaregeneration einer vorhandenen SQL-Datenbank oder eines Pools**

Wählen Sie für eine Datenbank auf der Seite „Übersicht“ den Link **Tarif** aus:

  ![Ändern der Hardware](./media/service-tiers-vcore/change-hardware.png)

Wählen Sie für einen Pool auf der Seite „Übersicht“ die Option **Konfigurieren** aus.

Führen Sie die Schritte zum Ändern der Konfiguration aus, und wählen Sie die Hardwaregeneration wie in den vorherigen Schritten beschrieben aus.

**So wählen Sie eine Hardwaregeneration beim Erstellen einer Instanz von SQL Managed Instance aus**

Ausführliche Informationen finden Sie unter [Erstellen einer Instanz von SQL Managed Instance](../managed-instance/instance-create-quickstart.md).

Wählen Sie auf der Registerkarte **Grundlagen** im Abschnitt **Compute und Speicher** den Link **Datenbank konfigurieren** aus, und wählen Sie dann die gewünschte Hardwaregeneration aus:

  ![SQL Managed Instance konfigurieren](./media/service-tiers-vcore/configure-managed-instance.png)
  
**So ändern Sie die Hardwaregeneration einer vorhandenen Instanz von SQL Managed Instance**

# <a name="the-azure-portal"></a>[Azure-Portal](#tab/azure-portal)

Klicken Sie auf der SQL Managed Instance-Seite im Abschnitt „Einstellungen“ auf den Link **Tarif** .

![SQL Managed Instance-Hardware ändern](./media/service-tiers-vcore/change-managed-instance-hardware.png)

Auf der Seite „Tarif“ können Sie die Hardwaregenerierung wie in den vorherigen Schritten beschrieben ändern.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie das folgende PowerShell-Skript:

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

Weitere Informationen finden Sie unter dem Befehl [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance).

# <a name="the-azure-cli"></a>[Die Azure-CLI](#tab/azure-cli)

Verwenden Sie den folgenden CLI-Befehl:

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

Weitere Informationen finden Sie unter dem Befehl [az sql mi update](/cli/azure/sql/mi#az-sql-mi-update).

---

### <a name="hardware-availability"></a>Hardwareverfügbarkeit

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a> Gen4/Gen5

Die Gen4-Hardware [wird eingestellt](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) und steht für neue Bereitstellungen nicht mehr zur Verfügung. Alle neuen Datenbanken müssen auf Gen5-Hardware bereitgestellt werden.

Gen5 ist in den meisten Regionen weltweit verfügbar.

#### <a name="fsv2-series"></a>Fsv2-Serie

Die Fsv2-Serie ist in den folgenden Regionen verfügbar: „Australien, Mitte“, „Australien, Mitte 2“, „Australien, Osten“, „Australien, Südosten“, „Brasilien, Süden“, „Kanada, Mitte“, „Asien, Osten“, „USA, Osten“, „Frankreich, Mitte“, „Indien, Mitte“, „Südkorea, Mitte“, „Südkorea, Süden“, „Europa, Norden“, „Südafrika, Norden“, „Asien, Südosten“, „Vereinigtes Königreich, Süden“, „Vereinigtes Königreich, Westen“, „Europa, Westen“, „USA, Westen 2“.


#### <a name="m-series"></a>M-Serie

Die M-Serie ist in den folgenden Regionen verfügbar: „USA, Osten“, „Europa, Norden“, „Europa, Westen“, „USA, Westen 2“.
<!--
M-series may also have limited availability in additional regions. You can request a different region than listed here, but fulfillment in a different region may not be possible.

To enable M-series availability in a subscription, access must be requested by [filing a new support request](#create-a-support-request-to-enable-m-series).


##### Create a support request to enable M-series: 

1. Select **Help + support** in the portal.
2. Select **New support request**.

On the **Basics** page, provide the following:

1. For **Issue type**, select **Service and subscription limits (quotas)**.
2. For **Subscription** = select the subscription to enable M-series.
3. For **Quota type**, select **SQL database**.
4. Select **Next** to go to the **Details** page.

On the **Details** page, provide the following:

1. In the **PROBLEM DETAILS** section select the **Provide details** link. 
2. For **SQL Database quota type** select **M-series**.
3. For **Region**, select the region to enable M-series.
    For regions where M-series is available, see [M-series availability](#m-series).

Approved support requests are typically fulfilled within 5 business days.
-->

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den ersten Schritten finden Sie unter: 
- [Erstellen einer SQL-Datenbank-Instanz mithilfe des Azure-Portals](single-database-create-quickstart.md)
- [Erstellen einer Instanz von SQL Managed Instance mithilfe des Azure-Portals](../managed-instance/instance-create-quickstart.md)

Ausführliche Informationen zu Preisen finden Sie auf der [Preisseite für Azure SQL-Datenbank](https://azure.microsoft.com/pricing/details/sql-database/single/).

Ausführliche Informationen zu den spezifischen Compute- und Speichergrößen der Dienstebenen „Universell“ und „Unternehmenskritisch“ finden Sie unter:

- [Ressourcenlimits des auf virtuellen Kernen basierenden Kaufmodells für Azure SQL-Datenbank](resource-limits-vcore-single-databases.md)
- [Ressourcenlimits des auf virtuellen Kernen basierenden Kaufmodells für Azure SQL-Datenbank](resource-limits-vcore-elastic-pools.md)
- [Ressourcenlimits des auf virtuellen Kernen basierenden Kaufmodells für Azure SQL Managed Instance](../managed-instance/resource-limits.md)