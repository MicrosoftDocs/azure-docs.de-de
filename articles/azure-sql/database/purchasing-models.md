---
title: Kaufmodelle
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Informieren Sie sich über die Kaufmodelle, die für Azure SQL-Datenbank und Azure SQL Managed Instance verfügbar sind.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/28/2020
ms.openlocfilehash: 8883263d6ddb2fb8ddc809f464288fcd282531bd
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92788824"
---
# <a name="choose-between-the-vcore-and-dtu-purchasing-models---azure-sql-database-and-sql-managed-instance"></a>Auswählen zwischen dem vCore-basierten und dem DTU-basierten Kaufmodell – Azure SQL-Datenbank und SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL-Datenbank und Azure SQL Managed Instance ermöglichen Ihnen den einfachen Kauf einer vollständig verwalteten PaaS-Datenbank-Engine (Platform-as-a-Service), die Ihre Anforderungen an Leistung und Kosten erfüllt. Je nach dem Bereitstellungsmodell, das Sie für Azure SQL-Datenbank ausgewählt haben, können Sie das Kaufmodell auswählen, das für Sie funktioniert:

- [Auf virtuellem Kern basierendes (vCore-basiertes) Kaufmodell](service-tiers-vcore.md) (empfohlen). Dieses Kaufmodell ermöglicht die Wahl zwischen einer bereitgestellten Computeebene und einer serverlosen Computeebene. Mit der bereitgestellten Computeebene wählen Sie die genaue Menge an Computeressourcen aus, die immer für Ihre Workload bereitgestellt werden. Mit der serverlosen Computeebene geben Sie die automatische Skalierung der Computeressourcen über einen konfigurierbaren Computebereich an. Mit dieser Computeebene haben Sie auch die Möglichkeit, die Datenbank entsprechend der Workloadaktivität automatisch anzuhalten und fortzusetzen. Der V-Kern-Einzelpreis pro Zeiteinheit ist auf der bereitgestellten Computeebene niedriger als auf der serverlosen Computeebene.
- [Auf Datenbanktransaktionseinheit basierendes (DTU-basiertes) Kaufmodell](service-tiers-dtu.md). Dieses Kaufmodell bietet gebündelte Compute- und Speicherpakete, die für gängige Workloads zusammengestellt wurden.

Es gibt zwei Kaufmodelle:

- Das [vCore-basierte Kaufmodell](service-tiers-vcore.md) ist sowohl für [Azure SQL-Datenbank](sql-database-paas-overview.md) als auch für [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) verfügbar. Die [Dienstebene „Hyperscale“](service-tier-hyperscale.md) ist für Einzeldatenbanken verfügbar, für die das [vCore-basierte Kaufmodell](service-tiers-vcore.md) verwendet wird.
- Das [DTU-basierte Kaufmodell](service-tiers-dtu.md) ist für [Azure SQL-Datenbank](single-database-manage.md) verfügbar.

In der folgenden Tabelle und im Diagramm werden die Kaufmodelle „V-Kern-basiertes Modell“ und „DTU-basiertes Modell“ verglichen und gegenübergestellt:

|**Kaufmodell**|**Beschreibung**|**Am besten geeignet für**|
|---|---|---|
|DTU-basiertes Modell|Dieses Modell basiert auf einem Paket mit Compute-, Speicher- und E/A-Ressourcen. Computegrößen werden für Einzeldatenbanken in DTUs und für Pools für elastische Datenbanken in elastischen Datenbanktransaktionseinheiten (eDTUs) ausgedrückt. Weitere Informationen zu DTUs und eDTUs finden Sie unter [Was sind DTUs und eDTUs?](purchasing-models.md#dtu-based-purchasing-model).|Kunden, die einfache, vorkonfigurierte Ressourcenoptionen wünschen|
|vCore-basiertes Modell|Mit diesem Modell können Sie Compute- und Speicherressourcen einzeln auswählen. Mit dem vCore-basierten Kaufmodell können Sie auch den [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/) für SQL Server nutzen, um Kosten einzusparen.|Kunden, die Flexibilität, Kontrolle und Transparenz schätzen|
||||  

![Vergleich der Preismodelle](./media/purchasing-models/pricing-model.png)

Möchten Sie Ihre Cloudausgaben optimieren und somit Geld sparen?

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="compute-costs"></a>Computekosten

### <a name="provisioned-compute-costs"></a>Bereitgestellte Computekosten

In der bereitgestellten Computeebene spiegeln die Computekosten die gesamte Computekapazität wider, die für die Anwendung bereitgestellt wird.

In der Dienstebene „Unternehmenskritisch“ werden mindestens drei Replikate automatisch zugeordnet. Um diese zusätzliche Zuordnung von Computeressourcen widerzuspiegeln, ist der Preis im vCore-basierten Kaufmodell in der Dienstebene „Unternehmenskritisch“ ungefähr 2,7 Mal höher als in der Dienstebene „Universell“. Entsprechend spiegelt der höhere Speicherpreis pro GB für die Dienstebene „Unternehmenskritisch“ die höheren E/A-Grenzwerte und die geringere Latenz des SSD-Speichers wider.

Die Kosten für Sicherungsspeicher sind für die Dienstebene „Unternehmenskritisch“ und die Dienstebene „Universell“ identisch, weil beide Ebenen Standardspeicher für Sicherungen verwenden.

### <a name="serverless-compute-costs"></a>Serverlose Computekosten

Eine Beschreibung, wie Computekapazität definiert ist und Kosten für die serverlose Computeebene berechnet werden, finden Sie unter [Azure SQL-Datenbank – Serverlos](serverless-tier-overview.md).

## <a name="storage-costs"></a>Speicherkosten

Unterschiedliche Arten von Speicher werden auch unterschiedlich berechnet. Für Datenspeicher wird Ihnen der bereitgestellte Speicher basierend auf der von Ihnen gewählten maximalen Datenbank- oder Poolgröße berechnet. Die Kosten ändern sich nicht, sofern Sie dieses Maximum nicht verringern oder erhöhen. Der Sicherungsspeicher ist mit den automatischen Sicherungen Ihrer Instanz verbunden und wird dynamisch zugeordnet. Durch eine Verlängerung des Aufbewahrungszeitraums für Ihre Sicherungen erhöht sich auch der von Ihrer Instanz verbrauchte Sicherungsspeicher.

Standardmäßig werden automatisierte Sicherungen Ihrer Datenbanken von einem Zeitraum von sieben Tagen in ein Blob-Standardspeicherkonto mit georedundantem Speicher mit Lesezugriff (Read-Access Geo-Redundant Storage, RA-GRS) kopiert. Dieser Speicher wird für wöchentliche vollständige Sicherungen, tägliche differenzielle Sicherungen und im 5-Minuten-Takt kopierte Sicherungen von Transaktionsprotokollen verwendet. Die Größen der Transaktionsprotokolle hängen von der Änderungsrate der Datenbank ab. Eine Mindestspeichermenge, die 100 Prozent der Datenbankgröße entspricht, wird kostenlos zur Verfügung gestellt. Zusätzlich verbrauchter Sicherungsspeicher wird pro GB und Monat abgerechnet.

Weitere Informationen zu den Preisen für Storage finden Sie auf der Seite [Azure SQL-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-database/single/).

## <a name="vcore-based-purchasing-model"></a>vCore-basiertes Kaufmodell

Ein virtueller Kern (V-Kern) repräsentiert eine logische CPU und bietet Ihnen die Möglichkeit, zwischen verschiedenen Hardwaregenerationen und den physischen Hardwaremerkmalen (z.B. der Anzahl der Kerne, dem Arbeitsspeicher, der Speichergröße) zu wählen. Beim vCore-basierten Kaufmodell erhalten Sie Flexibilität, Kontrolle und Transparenz in Bezug auf den individuellen Ressourcenverbrauch. Außerdem können Sie die lokalen Workloadanforderungen leicht auf die Cloud übertragen. Mit diesem Modell können Sie Compute-, Arbeitsspeicher- und Speicherressourcen entsprechend Ihren jeweiligen Workloadanforderungen auswählen.

Beim vCore-basierten Kaufmodell können Sie für SQL-Datenbank und SQL Managed Instance zwischen den Dienstebenen [Universell](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) und [Unternehmenskritisch](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) wählen.  Für Einzeldatenbanken können Sie auch die [Dienstebene „Hyperscale“](service-tier-hyperscale.md) auswählen.

Mit dem vCore-basierten Kaufmodell können Sie Compute- und Speicherressourcen einzeln auswählen, eine Leistung wie in Ihrer lokalen Umgebung erzielen und den Preis optimieren. Beim vCore-basierten Kaufmodell zahlen Sie für Folgendes:

- Computeressourcen (die Dienstebene + die Anzahl von V-Kernen und die Arbeitsspeichermenge + die Hardwaregeneration)
- Der Typ und die Menge von Daten und Protokollspeicher
- Sicherungsspeicher (RA-GRS)

> [!IMPORTANT]
> Computeressourcen, E/A sowie Daten- und Protokollspeicher werden pro Datenbank oder Pool für elastische Datenbanken berechnet. Sicherungsspeicher wird pro Datenbank berechnet. Weitere Informationen zu den Kosten für SQL Managed Instance finden Sie unter [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md).
> **Regionseinschränkungen:** Die aktuelle Liste der unterstützten Regionen finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Wenn Sie eine verwaltete Instanz in einer Region erstellen möchten, die derzeit nicht unterstützt wird, [senden Sie eine Supportanfrage über das Azure-Portal](quota-increase-request.md).

Wenn Ihre Datenbank mehr als 300 DTUs verbraucht, können Sie durch eine Umstellung auf das vCore-basierte Kaufmodell möglicherweise Ihre Kosten senken. Sie können für die Umstellung die von Ihnen bevorzugte API oder das Azure-Portal verwenden, ohne dass es zu Ausfallzeit kommt. Allerdings ist eine Umstellung nicht erforderlich und erfolgt nicht automatisch. Wenn das DTU-basierte Kaufmodell Ihre Leistungs- und Geschäftsanforderungen erfüllt, sollten Sie es weiter nutzen.

Informationen zum Wechsel vom DTU-basierten Kaufmodell zum vCore-basierten Kaufmodell finden Sie unter [Migrieren von DTU zu vCore](migrate-dtu-to-vcore.md).

## <a name="dtu-based-purchasing-model"></a>DTU-basiertes Kaufmodell

Eine Datenbanktransaktionseinheit (Database Transaction Unit, DTU) entspricht einer Mischung aus den Werten von CPU, Arbeitsspeicher, Lesevorgängen und Schreibvorgängen. Das DTU-basierte Kaufmodell verfügt über einen Satz mit vorkonfigurierten Paketen mit Computeressourcen und integriertem Speicher, um die Anwendungsleistung auf verschiedenen Ebenen zu erhöhen. Wenn Sie die Einfachheit eines vorkonfigurierten Pakets und von festen Monatszahlungen bevorzugen, ist das DTU-basierte Modell möglicherweise besser für ihre Anforderungen geeignet.

Beim DTU-basierten Kaufmodell können Sie zwischen den Dienstebenen „Basic“, „Standard“ und „Premium“ für Azure SQL-Datenbank wählen. Das DTU-basierte Kaufmodell ist für Azure SQL Managed Instance nicht verfügbar.

### <a name="database-transaction-units-dtus"></a>Datenbanktransaktionseinheiten (DTUs)

Für eine einzelne Datenbank in einer bestimmten Computegröße innerhalb einer [Dienstebene](single-database-scale.md) garantiert Azure einen bestimmten Ressourcenumfang für diese Datenbank (unabhängig von jeder anderen Datenbank in der Azure-Cloud). Diese Garantie bietet eine vorhersagbare Leistungsebene. Der Umfang der Ressourcen, die einer Datenbank zugeordnet werden, wird als Anzahl von DTUs berechnet. Es handelt sich dabei um ein Paket aus Compute-, Speicher- und E/A-Ressourcen.

Das Verhältnis zwischen diesen Ressourcen wurde ursprünglich anhand einer [OLTP-Benchmark-Workload](service-tiers-dtu.md) ermittelt, die für realistische Onlinetransaktionsverarbeitungs-Workloads (Online Transaction Processing, OLTP) typisch ist. Wenn Ihre Workload den Umfang einer dieser Ressourcen überschreitet, wird der Durchsatz gedrosselt, wodurch eine niedrigere Leistung und Timeouts verursacht werden.

Die von Ihrer Workload verwendeten Ressourcen wirken sich nicht auf die Ressourcen aus, die für andere Datenbanken in der Azure-Cloud verfügbar sind. Umgekehrt wirken sich die von anderen Workloads verwendeten Ressourcen nicht auf die Ressourcen aus, die für Ihre Datenbank verfügbar sind.

![Begrenzungsrahmen](./media/purchasing-models/bounding-box.png)

DTUs sind besonders aufschlussreich, um die relativen Ressourcen zu verstehen, die für Datenbanken in unterschiedlichen Computegrößen und Dienstebenen zugeordnet werden. Beispiel:

- Eine Verdoppelung der DTUs durch Erhöhen der Computegröße einer Datenbank entspricht einer Verdoppelung des Satzes von Ressourcen, die dieser Datenbank zur Verfügung stehen.
- Eine P11-Datenbank in der Dienstebene „Premium“ mit 1750 DTUs bietet eine 350 Mal höhere DTU-Computeleistung als eine Datenbank in der Dienstebene „Basic“mit fünf DTUs.  

Um einen tieferen Einblick in den Ressourcenverbrauch (DTU) Ihrer Workload zu erhalten, verwenden Sie die [Statistik zur Abfrageleistung](query-performance-insight-use.md) für die folgenden Aufgaben:

- Ermitteln der häufigsten Abfragen nach den Werten von CPU-Nutzung/Dauer/Ausführungshäufigkeit, bei denen unter Umständen eine Leistungssteigerung erzielt werden kann. Beispielsweise könnte eine E/A-intensive Abfrage von [In-Memory-Optimierungstechniken](../in-memory-oltp-overview.md) profitieren, um den verfügbaren Arbeitsspeicher in einer bestimmten Dienstebene und einer bestimmten Computegröße besser zu nutzen.
- Analysieren der Details einer Abfrage, um deren Text und deren Verlauf der Ressourcennutzung anzuzeigen.
- Zugreifen auf Empfehlungen zur Leistungsoptimierung, die Aktionen erläutern, die vom [SQL Database Advisor](database-advisor-implement-performance-recommendations.md) unternommen werden.

### <a name="elastic-database-transaction-units-edtus"></a>Transaktionseinheiten in elastischer Datenbank (eDTUs)

Sie können Datenbanken, die immer verfügbar sind, in einem [Pool für elastische Datenbanken](elastic-pool-overview.md) platzieren, statt eine dedizierte Menge von Ressourcen (DTUs) für sie bereitzustellen, die nicht immer benötigt werden. Die Datenbanken in einem Pool für elastische Datenbanken befinden sich auf einem einzigen Server und nutzen gemeinsam einen Pool von Ressourcen.

Die gemeinsam genutzten Ressourcen in einem Pool für elastische Datenbanken werden als elastische Datenbanktransaktionseinheiten (elastic Database Transaction Units, eDTUs) gemessen. Pools für elastische Datenbanken stellen eine einfache und kostengünstige Lösung dar, um Leistungsziele für mehrere Datenbanken mit unterschiedlichsten und unvorhersehbaren Nutzungsmustern zu verwalten. Mit einem Pool für elastische Datenbanken wird sichergestellt, dass eine einzelne Datenbank im Pool nicht alle Ressourcen nur für sich nutzen kann, wodurch dafür gesorgt ist, dass jeder Datenbank im Pool immer eine Mindestmenge an erforderlichen Ressourcen zur Verfügung steht.

Ein Pool erhält eine festgelegte Anzahl von eDTUs zu einem festen Preis. Im Pool für elastische Datenbanken können einzelne Datenbanken die automatische Skalierung innerhalb der konfigurierten Grenzen ausführen. Eine Datenbank mit einer höheren Last nutzt mehr eDTUs, um die Anforderungen zu erfüllen. Datenbanken mit geringerer Last nutzen weniger eDTUs. Datenbanken ohne Auslastung verwenden keine eDTUs. Weil Ressourcen für den gesamten Pool statt pro Datenbank bereitgestellt werden, vereinfacht ein Pool für elastische Datenbanken Ihre Verwaltungsaufgaben, und bietet dieser Pool ein vorhersagbares Budget für den Pool.

Sie können einem vorhandenen Pool weitere eDTUs hinzufügen, ohne dass es zu Datenbankausfällen und Auswirkungen auf die Datenbanken im Pool kommt. Umgekehrt können Sie jederzeit zusätzliche eDTUs, die Sie nicht mehr benötigen, aus einem vorhandenen Pool entfernen. Außerdem können Sie jederzeit Datenbanken zu einem Pool hinzufügen oder aus einem Pool entfernen. Um eDTUs für andere Datenbanken zu reservieren, begrenzen Sie die Anzahl der eDTUs, die eine Datenbank unter hoher Last verwenden kann. Wenn eine Datenbank die entsprechenden Ressourcen durchgängig zu wenig nutzt, sollten Sie sie aus dem Pool entfernen und als einzelne Datenbank mit der voraussichtlichen Menge an erforderlichen Ressourcen konfigurieren.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Ermitteln der für eine Workload erforderlichen Anzahl von DTUs

Wenn Sie die vorhandene Workload eines lokalen oder virtuellen SQL Server-Computers zu SQL-Datenbank migrieren möchten, verwenden Sie den [DTU-Rechner](https://dtucalculator.azurewebsites.net/), um die ungefähre Anzahl von benötigten DTUs zu berechnen. Für eine vorhandene SQL-Datenbank-Workload verwenden Sie die [Statistik zur Abfrageleistung](query-performance-insight-use.md), um den Ressourcenverbrauch (DTUs) Ihrer Datenbank zu verstehen und einen tieferen Einblick zur Optimierung Ihrer Workload zu gewinnen. In der dynamischen Verwaltungssicht (Dynamic Management View, DMV) [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) können Sie den Ressourcenverbrauch der letzten Stunde anzeigen. In der Katalogansicht [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) wird der Ressourcenverbrauch der letzten 14 Tage angezeigt. Da hierfür Durchschnittswerte für jeweils fünf Minuten genutzt werden, ist jedoch die Genauigkeit geringer.

### <a name="determine-dtu-utilization"></a>Ermitteln der DTU-Nutzung

Verwenden Sie die folgende Formel, um den durchschnittlichen Prozentsatz der DTU-/eDTU-Nutzung in Relation zum DTU-/eDTU-Grenzwert einer Datenbank oder eines Pools für elastische Datenbanken zu bestimmen:

`avg_dtu_percent = MAX(avg_cpu_percent, avg_data_io_percent, avg_log_write_percent)`

Die Eingabewerte für diese Formel können aus den DMVs [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) und [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) abgerufen werden. Anders ausgedrückt: Um den Prozentsatz der DTU-/eDTU-Nutzung für den DTU-/eDTU-Grenzwert einer Datenbank oder eines Pools für elastische Datenbanken zu ermitteln, wählen Sie den größten Prozentwert aus `avg_cpu_percent`, `avg_data_io_percent` und `avg_log_write_percent` zu einem bestimmten Zeitpunkt aus.

> [!NOTE]
> Der DTU-Grenzwert einer Datenbank wird durch die für die Datenbank verfügbaren Werte von CPU, Lesevorgängen, Schreibvorgängen und Arbeitsspeicher bestimmt. Da die SQL-Datenbank-Engine jedoch in der Regel den gesamten verfügbaren Arbeitsspeicher für den Datencache verwendet, um die Leistung zu verbessern, liegt der `avg_memory_usage_percent`-Wert unabhängig von der aktuellen Datenbankauslastung normalerweise nahe bei 100 Prozent. Daher wird der Arbeitsspeicher nicht in der DTU-Nutzungsformel verwendet, obwohl er den DTU-Grenzwert indirekt beeinflusst.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Workloads, die von einem elastischen Ressourcenpool profitieren

Pools eignen sich sehr gut für Datenbanken mit einer geringen durchschnittlichen Ressourcennutzung und mit relativ seltenen Nutzungsspitzen. Weitere Informationen finden Sie unter [Wann sollten Sie einen Pool für elastische SQL-Datenbank-Instanzen erwägen?](elastic-pool-overview.md)

### <a name="hardware-generations-in-the-dtu-based-purchasing-model"></a>Hardwaregenerationen im DTU-basierten Kaufmodell

Im DTU-basierten Kaufmodell können Kunden die für ihre Datenbanken verwendete Hardwaregeneration nicht wählen. Während eine bestimmte Datenbank normalerweise für einen längeren Zeitraum (in der Regel mehrere Monate) auf einer bestimmten Hardwaregeneration verbleibt, gibt es bestimmte Ereignisse, die dazu führen können, dass eine Datenbank auf eine andere Hardwaregeneration verschoben wird.

Beispielsweise kann eine Datenbank auf eine andere Hardwaregeneration verschoben werden, wenn sie auf ein anderes Dienstziel hoch- oder herunterskaliert wird, oder aber wenn die aktuelle Infrastruktur in einem Rechenzentrum ihre Kapazitätsgrenzen erreicht oder die aktuell verwendete Hardware aufgrund ihres Lebenszyklus außer Betrieb genommen wird.

Wenn eine Datenbank auf eine andere Hardware verschoben wird, kann sich die Workloadleistung ändern. Das DTU-Modell gewährleistet, dass der Durchsatz und die Reaktionszeit der [DTU-Benchmark](./service-tiers-dtu.md#dtu-benchmark)-Workload im Wesentlichen identisch bleiben, wenn die Datenbank auf eine andere Hardwaregeneration verschoben wird, solange das Dienstziel (die Anzahl der DTUs) unverändert bleibt.

Allerdings können die Auswirkungen der Verwendung unterschiedlicher Hardware für dasselbe Dienstziel über das gesamte Spektrum der in Azure SQL-Datenbank ausgeführten Kundenworkloads ausgeprägter sein. Unterschiedliche Workloads profitieren von unterschiedlichen Hardwarekonfigurationen und Features. Deshalb ist es für andere Workloads als der DTU-Benchmark möglich, Leistungsunterschiede zu erkennen, wenn die Datenbank von einer Hardwaregeneration auf eine andere verschoben wird.

So ist beispielsweise die Leistung bei einer Anwendung, die von Netzwerklatenz abhängig ist, auf einer Gen5-Hardware besser als auf Gen4 aufgrund der Nutzung von beschleunigtem Netzwerkbetrieb in Gen5, während bei einer Anwendung mit intensiven E/A-Lesevorgängen die Leistung auf Gen4-Hardware aufgrund eines höheren Arbeitsspeicher-pro-Kern-Verhältnisses auf Gen4 besser ist als auf Gen5.

Kunden mit Workloads, die von Hardwareänderungen abhängig sind, oder Kunden, die die Auswahl der Hardwaregeneration für ihre Datenbank steuern möchten, können das [Kaufmodell für virtuelle Kerne](service-tiers-vcore.md) verwenden, um beim Erstellen und Skalieren von Datenbanken ihre bevorzugte Hardwaregeneration auszuwählen. Im Modell „V-Kern“ werden Ressourcenlimits der jeweiligen Dienstziele auf jeder Hardwaregeneration dokumentiert – sowohl für [Einzeldatenbanken](resource-limits-vcore-single-databases.md) (Singletons) als auch für [Pools für elastische Datenbanken](resource-limits-vcore-elastic-pools.md). Weitere Informationen zu Hardwaregenerationen im vCore-basierten Modell finden Sie unter [Hardwaregenerationen](./service-tiers-vcore.md#hardware-generations).

## <a name="frequently-asked-questions-faqs"></a>Häufig gestellte Fragen (FAQs)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>Muss ich meine Anwendung offline schalten, um von einer DTU-basierten Dienstebene auf eine V-Kern-basierte Dienstebene umzustellen?

Nein. Sie müssen die Anwendung dafür nicht offline schalten. Die neuen Dienstebenen bieten eine einfache Onlinekonvertierungsmethode, die dem vorhandenen Prozess zur Durchführung eines Upgrades für Datenbanken von der Dienstebene „Standard“ auf „Premium“ und andersherum ähnelt. Sie können diese Konvertierung über das Azure-Portal, über PowerShell, über die Azure CLI, über T-SQL oder über die REST-API starten. Weitere Informationen finden Sie unter [Verwalten von Ressourcen für eine einzelne Datenbank in Azure SQL-Datenbank](single-database-scale.md) und [Pools für elastische Datenbanken als Hilfe beim Verwalten und Skalieren mehrerer Azure SQL-Datenbank-Instanzen](elastic-pool-overview.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Kann ich eine Datenbank von einer Dienstebene im vCore-basierten Kaufmodell auf eine Dienstebene im DTU-basierten Kaufmodell konvertieren?

Ja. Sie können Ihre Datenbank leicht in ein beliebiges unterstütztes Leistungsziel konvertieren, indem Sie das Azure-Portal, PowerShell, die Azure CLI, T-SQL oder die REST-API verwenden. Weitere Informationen finden Sie unter [Verwalten von Ressourcen für eine einzelne Datenbank in Azure SQL-Datenbank](single-database-scale.md) und [Pools für elastische Datenbanken als Hilfe beim Verwalten und Skalieren mehrerer Azure SQL-Datenbank-Instanzen](elastic-pool-overview.md).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu dem vCore-basierten Kaufmodell finden Sie unter [vCore-basiertes Kaufmodell](service-tiers-vcore.md).
- Weitere Informationen zu dem DTU-basierten Kaufmodell finden Sie unter [DTU-basiertes Kaufmodell](service-tiers-dtu.md).