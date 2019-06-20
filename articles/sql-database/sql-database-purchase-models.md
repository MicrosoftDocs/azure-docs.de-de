---
title: Kaufmodelle für Azure SQL-Datenbank | Microsoft-Dokumentation
description: Erhalten Sie Informationen zu den Kaufmodellen, die für Azure SQL-Datenbank verfügbar sind.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 04/26/2019
ms.openlocfilehash: 98c19732c372fbcda3ca8e746d002f94c2687b22
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431369"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>Wählen zwischen den Kaufmodellen „V-Kern“ und „DTU“

Azure SQL-Datenbank ermöglicht Ihnen den einfachen Kauf einer vollständig verwalteten PaaS-Datenbank-Engine (Platform-as-a-Service), die Ihre Anforderungen an Leistung und Kosten erfüllt. Je nach dem Bereitstellungsmodell, das Sie für Azure SQL-Datenbank ausgewählt haben, können Sie das Kaufmodell auswählen, das für Sie funktioniert:

- [Auf virtuellem Kern basierendes (vCore-basiertes) Kaufmodell](sql-database-service-tiers-vcore.md) (empfohlen). Dieses Kaufmodell ermöglicht die Wahl zwischen einer bereitgestellten Computeebene und einer serverlosen Computeebene (Vorschauversion). Mit der bereitgestellten Computeebene wählen Sie die genaue Menge an Computeressourcen aus, die immer für Ihre Workload bereitgestellt werden. Mit der serverlosen Computeebene geben Sie die automatische Skalierung der Computeressourcen über einen konfigurierbaren Computebereich an. Mit dieser Computeebene haben Sie auch die Möglichkeit, die Datenbank entsprechend der Workloadaktivität automatisch anzuhalten und fortzusetzen. Der V-Kern-Einzelpreis pro Zeiteinheit ist auf der bereitgestellten Computeebene niedriger als auf der serverlosen Computeebene.
- [Auf Datenbanktransaktionseinheit basierendes (DTU-basiertes) Kaufmodell](sql-database-service-tiers-dtu.md). Dieses Kaufmodell bietet gebündelte Compute- und Speicherpakete, die für gängige Workloads zusammengestellt wurden.

Es sind unterschiedliche Kaufmodelle für unterschiedliche Azure SQL-Datenbank-Bereitstellungsmodelle verfügbar:

- Die Bereitstellungsoptionen [Einzeldatenbank](sql-database-single-databases-manage.md) und [Pools für elastische Datenbanken](sql-database-elastic-pool.md) in [Azure SQL-Datenbank](sql-database-technical-overview.md) bieten sowohl das [DTU-basierte Kaufmodell](sql-database-service-tiers-dtu.md) als auch das [vCore-basierte Kaufmodell](sql-database-service-tiers-vcore.md) an.
- Die Bereitstellungsoption [Verwaltete Instanz](sql-database-managed-instance.md) in Azure SQL-Datenbank bietet nur das [vCore-basierte Kaufmodell](sql-database-service-tiers-vcore.md).
- Die [Dienstebene „Hyperscale“](sql-database-service-tier-hyperscale.md) ist für Einzeldatenbanken verfügbar, für die das [vCore-basierte Kaufmodell](sql-database-service-tiers-vcore.md) verwendet wird.

In der folgenden Tabelle und im Diagramm werden die Kaufmodelle „V-Kern-basiertes Modell“ und „DTU-basiertes Modell“ verglichen und gegenübergestellt:

|**Kaufmodell**|**Beschreibung**|**Am besten geeignet für**|
|---|---|---|
|DTU-basiertes Modell|Dieses Modell basiert auf einem Paket mit Compute-, Speicher- und E/A-Ressourcen. Computegrößen werden für Einzeldatenbanken in DTUs und für Pools für elastische Datenbanken in elastischen Datenbanktransaktionseinheiten (eDTUs) ausgedrückt. Weitere Informationen zu DTUs und eDTUs finden Sie unter [Was sind DTUs und eDTUs?](sql-database-purchase-models.md#dtu-based-purchasing-model).|Eignet sich am besten für Kunden, die einfache, vorkonfigurierte Ressourcenoptionen benötigen.|
|V-Kern-basiertes Modell|Mit diesem Modell können Sie Compute- und Speicherressourcen einzeln auswählen. Mit dem vCore-basierten Kaufmodell können Sie auch den [Azure-Hybridvorteil für SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) nutzen, um Kosten einzusparen.|Eignet sich am besten für Kunden, für die Flexibilität, Kontrolle und Transparenz im Vordergrund stehen.|
||||  

![Vergleich der Preismodelle](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Computekosten

### <a name="provisioned-compute-costs"></a>Bereitgestellte Computekosten

In der bereitgestellten Computeebene spiegeln die Computekosten die gesamte Computekapazität wider, die für die Anwendung bereitgestellt wird.

Für die Dienstebene „Unternehmenskritisch“ werden mindestens drei Replikate automatisch zugeordnet. Um diese zusätzliche Zuordnung von Computeressourcen widerzuspiegeln, ist der Preis im vCore-basierten Kaufmodell für die Dienstebene „Unternehmenskritisch“ ungefähr 2,7 Mal höher als für die Dienstebene „Universell“. Analog entspricht der höhere Speicherpreis pro GB für die Dienstebene „Unternehmenskritisch“ dem höheren E/A-Wert und der geringeren Wartezeit des SSD-Speichers.

Die Kosten für Sicherungsspeicher sind für die Dienstebene „Unternehmenskritisch“ und die Dienstebene „Universell“ identisch, da für beide Ebenen Standardspeicher verwendet wird.

### <a name="serverless-compute-costs"></a>Serverlose Computekosten

Eine Beschreibung, wie Computekapazität definiert ist und Kosten für die serverlose Computeebene berechnet werden, finden Sie unter [SQL-Datenbank – serverlos (Vorschau)](sql-database-serverless.md).

## <a name="storage-costs"></a>Speicherkosten

Unterschiedliche Arten von Speicher werden auch unterschiedlich berechnet. Für Datenspeicher wird Ihnen der bereitgestellte Speicher basierend auf der von Ihnen gewählten maximalen Datenbank- oder Poolgröße berechnet. Die Kosten ändern sich nicht, sofern Sie dieses Maximum nicht verringern oder erhöhen. Der Sicherungsspeicher ist mit den automatischen Sicherungen Ihrer Instanz verbunden und wird dynamisch zugeordnet. Durch eine Verlängerung der Beibehaltungsdauer Ihrer Sicherungen erhöht sich auch der von Ihrer Instanz verbrauchte Sicherungsspeicher.

Standardmäßig werden automatisierte Sicherungen Ihrer Datenbanken für einen Zeitraum von 7 Tagen in ein Standard-BLOB-Speicherkonto mit georedundantem Speicher mit Lesezugriff (RA-GRS) kopiert. Dieser Speicher wird für wöchentliche vollständige Sicherungen, tägliche differenzielle Sicherungen und im 5-Minuten-Takt kopierte Sicherungen von Transaktionsprotokollen verwendet. Die Größen der Transaktionsprotokolle hängen von der Änderungsrate der Datenbank ab. Eine Mindestspeichermenge, die 100 Prozent der Datenbankgröße entspricht, wird kostenlos zur Verfügung gestellt. Zusätzlich verbrauchter Sicherungsspeicher wird pro GB und Monat abgerechnet.

Weitere Informationen zu den Preisen für Storage finden Sie auf der Seite [Azure SQL-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-database/single/).

## <a name="vcore-based-purchasing-model"></a>vCore-basiertes Kaufmodell

Ein virtueller Kern (V-Kern) repräsentiert eine logische CPU und bietet Ihnen die Möglichkeit, zwischen verschiedenen Hardwaregenerationen und den physischen Hardwaremerkmalen (z.B. der Anzahl der Kerne, dem Arbeitsspeicher, der Speichergröße) zu wählen. Beim vCore-basierten Kaufmodell erhalten Sie Flexibilität, Kontrolle und Transparenz in Bezug auf den individuellen Ressourcenverbrauch. Außerdem können Sie die lokalen Workloadanforderungen leicht auf die Cloud übertragen. Mit diesem Modell können Sie Compute-, Arbeitsspeicher- und Speicherressourcen entsprechend Ihren jeweiligen Workloadanforderungen auswählen.

Beim vCore-basierten Kaufmodell können Sie für [Einzeldatenbanken](sql-database-single-database-scale.md), [Pools für elastische Datenbanken](sql-database-elastic-pool.md) und [verwaltete Instanzen](sql-database-managed-instance.md) zwischen den Dienstebenen [Universell](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) und [Unternehmenskritisch](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) wählen. Für Einzeldatenbanken können Sie auch die [Dienstebene „Hyperscale“](sql-database-service-tier-hyperscale.md) auswählen.

Mit dem vCore-basierten Kaufmodell können Sie Compute- und Speicherressourcen einzeln auswählen, eine Leistung wie in Ihrer lokalen Umgebung erzielen und den Preis optimieren. Beim vCore-basierten Kaufmodell zahlen Sie für Folgendes:

- Computeressourcen (die Dienstebene + die Anzahl von V-Kernen und die Arbeitsspeichermenge + die Hardwaregeneration)
- Der Typ und die Menge von Daten und Protokollspeicher
- Sicherungsspeicher (RA-GRS)

> [!IMPORTANT]
> Computeressourcen, E/A sowie Daten- und Protokollspeicher werden pro Datenbank oder Pool für elastische Datenbanken berechnet. Sicherungsspeicher wird pro Datenbank berechnet. Weitere Informationen zu den Kosten für verwaltete Instanzen finden Sie unter [Verwaltete Instanzen](sql-database-managed-instance.md).
> **Regionseinschränkungen:** Die aktuelle Liste der unterstützten Regionen finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Wenn Sie eine verwaltete Instanz in einer Region erstellen möchten, die derzeit nicht unterstützt wird, [senden Sie eine Supportanfrage über das Azure-Portal](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance).

Wenn Ihre Einzeldatenbank oder Ihr Pool für elastische Datenbanken mehr als 300 DTUs verbraucht, können Sie Ihre Kosten durch eine Umstellung auf das vCore-basierte Kaufmodell möglicherweise senken. Sie können für die Umstellung die von Ihnen bevorzugte API oder das Azure-Portal verwenden, ohne dass es zu Ausfallzeit kommt. Allerdings ist eine Umstellung nicht erforderlich und erfolgt nicht automatisch. Wenn das DTU-basierte Kaufmodell Ihre Leistungs- und Geschäftsanforderungen erfüllt, sollten Sie es weiter nutzen.

Wenn Sie vom DTU-basierten Kaufmodell auf das vCore-basierte Kaufmodell umstellen möchten, wählen Sie die Computegröße entsprechend den folgenden Faustregeln aus:

- Jeweils 100 DTUs im Standard-Tarif erfordern mindestens 1 virtuellen Kern in der Dienstebene „Universell“.
- Jeweils 125 DTUs im Premium-Tarif erfordern mindestens 1 virtuellen Kern in der Dienstebene „Unternehmenskritisch“.

## <a name="dtu-based-purchasing-model"></a>DTU-basiertes Kaufmodell

Eine Datenbanktransaktionseinheit (Database Transaction Unit, DTU) entspricht einer Mischung aus den Werten von CPU, Arbeitsspeicher, Lesevorgängen und Schreibvorgängen. Das DTU-basierte Kaufmodell verfügt über einen Satz mit vorkonfigurierten Paketen mit Computeressourcen und integriertem Speicher, um die Anwendungsleistung auf verschiedenen Ebenen zu erhöhen. Wenn Sie die Einfachheit eines vorkonfigurierten Pakets und von festen Monatszahlungen bevorzugen, ist das DTU-basierte Modell möglicherweise besser für ihre Anforderungen geeignet.

Beim DTU-basierten Kaufmodell können Sie zwischen den Dienstebenen „Basic“, „Standard“ und „Premium“ sowohl für [Einzeldatenbanken](sql-database-single-database-scale.md) als auch für [Pools für elastische Datenbanken](sql-database-elastic-pool.md) wählen. Das DTU-basierte Kaufmodell ist für [verwaltete Instanzen](sql-database-managed-instance.md) nicht verfügbar.

### <a name="database-transaction-units-dtus"></a>Datenbanktransaktionseinheiten (DTUs)

Für eine einzelne Datenbank in einer bestimmten Computegröße innerhalb einer [Dienstebene](sql-database-single-database-scale.md) garantiert Microsoft einen bestimmten Ressourcenumfang für diese Datenbank (unabhängig von jeder anderen Datenbank in der Azure-Cloud). Diese Garantie bietet eine vorhersagbare Leistungsebene. Der Umfang der Ressourcen, die einer Datenbank zugeordnet werden, wird als Anzahl von DTUs berechnet. Es handelt sich dabei um ein Paket aus Compute-, Speicher- und E/A-Ressourcen.

Das Verhältnis zwischen diesen Ressourcen wurde ursprünglich anhand einer [OLTP-Benchmark-Workload](sql-database-benchmark-overview.md) ermittelt, die für realistische Onlinetransaktionsverarbeitungs-Workloads (Online Transaction Processing, OLTP) typisch ist. Wenn Ihre Workload den Umfang einer dieser Ressourcen überschreitet, wird der Durchsatz gedrosselt, wodurch eine niedrigere Leistung und Timeouts verursacht werden.

Die Ressourcen, die von Ihrer Workload verwendet werden, wirken sich nicht auf die Ressourcen aus, die für andere SQL-Datenbanken in der Azure-Cloud verfügbar sind. Umgekehrt wirken sich die Ressourcen, die von anderen Workloads verwendet werden, nicht auf die Ressourcen aus, die für Ihre SQL-Datenbank verfügbar sind.

![Begrenzungsrahmen](./media/sql-database-what-is-a-dtu/bounding-box.png)

DTUs sind besonders nützlich, um die relativen Ressourcen zu verstehen, die für Azure SQL-Datenbanken bei unterschiedlichen Computegrößen und Diensttarifen zugeordnet werden. Beispiel:

- Eine Verdoppelung der DTUs durch Erhöhen der Computegröße einer Datenbank entspricht einer Verdoppelung des Satzes von Ressourcen, die dieser Datenbank zur Verfügung stehen.
- Eine Premium-Dienstebene-P11-Datenbank mit 1750 DTUs bietet eine 350 Mal höhere DTU-Computeleistung als eine Basic-Dienstebene-Datenbank mit fünf DTUs.  

Um einen tieferen Einblick in den Ressourcenverbrauch (DTU) Ihrer Workload zu erhalten, verwenden Sie die [Statistik zur Abfrageleistung](sql-database-query-performance.md) für die folgenden Aufgaben:

- Ermitteln der häufigsten Abfragen nach den Werten von CPU-Nutzung/Dauer/Ausführungshäufigkeit, bei denen unter Umständen eine Leistungssteigerung erzielt werden kann. Beispielsweise könnte eine E/A-intensive Abfrage von [In-Memory-Optimierungstechniken](sql-database-in-memory.md) profitieren, um den verfügbaren Arbeitsspeicher in einer bestimmten Dienstebene und einer bestimmten Computegröße besser zu nutzen.
- Analysieren der Details einer Abfrage, um deren Text und deren Verlauf der Ressourcennutzung anzuzeigen.
- Zugreifen auf Empfehlungen zur Leistungsoptimierung, die Aktionen erläutern, die vom [SQL Database Advisor](sql-database-advisor.md) unternommen werden.

### <a name="elastic-database-transaction-units-edtus"></a>Transaktionseinheiten in elastischer Datenbank (eDTUs)

Für SQL-Datenbanken, die immer verfügbar sind, können Sie diese Datenbanken, statt eine dedizierte Menge von Ressourcen (DTUs) bereitzustellen, die nicht immer benötigt werden, in einem [Pool für elastische Datenbanken](sql-database-elastic-pool.md) platzieren. Die Datenbanken in einem Pool für elastische Datenbanken befinden sich auf einem einzelnen Azure SQL-Datenbank-Server und nutzen gemeinsam einen Pool von Ressourcen.

Die gemeinsam genutzten Ressourcen in einem Pool für elastische Datenbanken werden als elastische Datenbanktransaktionseinheiten (elastic Database Transaction Units, eDTUs) gemessen. Pools für elastische Datenbanken stellen eine einfache und kostengünstige Lösung dar, um Leistungsziele für mehrere Datenbanken mit unterschiedlichsten und unvorhersehbaren Nutzungsmustern zu verwalten. Mit einem Pool für elastische Datenbanken wird sichergestellt, dass eine einzelne Datenbank im Pool nicht alle Ressourcen nur für sich nutzen kann, wodurch dafür gesorgt ist, dass jeder Datenbank im Pool immer eine Mindestmenge an erforderlichen Ressourcen zur Verfügung steht.

Ein Pool erhält eine festgelegte Anzahl von eDTUs zu einem festen Preis. Im Pool für elastische Datenbanken können einzelne Datenbanken die automatische Skalierung innerhalb der konfigurierten Grenzen ausführen. Eine Datenbank mit einer höheren Last nutzt mehr eDTUs, um die Anforderungen zu erfüllen. Datenbanken mit geringerer Last nutzen weniger eDTUs. Datenbanken ohne Auslastung verwenden keine eDTUs. Weil Ressourcen für den gesamten Pool statt pro Datenbank bereitgestellt werden, vereinfacht ein Pool für elastische Datenbanken Ihre Verwaltungsaufgaben, und bietet dieser Pool ein vorhersagbares Budget für den Pool.

Sie können einem vorhandenen Pool weitere eDTUs hinzufügen, ohne dass es zu Datenbankausfällen und Auswirkungen auf die Datenbanken im Pool kommt. Umgekehrt können Sie jederzeit zusätzliche eDTUs, die Sie nicht mehr benötigen, aus einem vorhandenen Pool entfernen. Außerdem können Sie jederzeit Datenbanken zu einem Pool hinzufügen oder aus einem Pool entfernen. Um eDTUs für andere Datenbanken zu reservieren, begrenzen Sie die Anzahl der eDTUs, die eine Datenbank unter hoher Last verwenden kann. Wenn eine Datenbank die entsprechenden Ressourcen durchgängig zu wenig nutzt, sollten Sie sie aus dem Pool entfernen und als einzelne Datenbank mit der voraussichtlichen Menge an erforderlichen Ressourcen konfigurieren.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Ermitteln der für eine Workload erforderlichen Anzahl von DTUs

Wenn Sie die vorhandene Workload eines lokalen oder virtuellen SQL Server-Computers zu Azure SQL-Datenbank migrieren möchten, verwenden Sie den [DTU-Rechner](https://dtucalculator.azurewebsites.net/), um die ungefähre Anzahl von benötigten DTUs zu berechnen. Für eine vorhandene Azure SQL-Datenbank-Workload verwenden Sie die [Statistik zur Abfrageleistung](sql-database-query-performance.md), um den Ressourcenverbrauch (DTUs) Ihrer Datenbank zu verstehen und einen tieferen Einblick zur Optimierung Ihrer Workload zu gewinnen. In der dynamischen Verwaltungssicht (Dynamic Management View, DMV) [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) wird der Ressourcenverbrauch der letzten Stunde angezeigt. In der Katalogansicht [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) wird der Ressourcenverbrauch der letzten 14 Tage angezeigt. Da hierfür Durchschnittswerte für jeweils fünf Minuten genutzt werden, ist jedoch die Genauigkeit geringer.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Workloads, die von einem elastischen Ressourcenpool profitieren

Pools eignen sich sehr gut für Datenbanken mit einer geringen durchschnittlichen Ressourcennutzung und mit relativ seltenen Nutzungsspitzen. SQL-Datenbank ermittelt automatisch den Verlauf der Ressourcennutzung von Datenbanken auf einem vorhandenen SQL-Datenbank-Server und empfiehlt eine geeignete Poolkonfiguration im Azure-Portal. Weitere Informationen finden Sie unter [Wann sollten Sie einen Pool für elastische SQL-Datenbank-Instanzen erwägen?](sql-database-elastic-pool.md)

## <a name="frequently-asked-questions-faqs"></a>Häufig gestellte Fragen (FAQs)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>Muss ich meine Anwendung offline schalten, um von einer DTU-basierten Dienstebene auf eine V-Kern-basierte Dienstebene umzustellen?

Nein. Sie müssen die Anwendung dafür nicht offline schalten. Die neuen Dienstebenen bieten eine einfache Onlinekonvertierungsmethode, die dem vorhandenen Prozess zur Durchführung eines Upgrades für Datenbanken von der Dienstebene „Standard“ auf „Premium“ und andersherum ähnelt. Sie können diese Konvertierung über das Azure-Portal, über PowerShell, über die Azure CLI, über T-SQL oder über die REST-API starten. Weitere Informationen finden Sie unter [Verwalten von Ressourcen für eine einzelne Datenbank in Azure SQL-Datenbank](sql-database-single-database-scale.md) und [Pools für elastische Datenbanken als Hilfe beim Verwalten und Skalieren mehrerer Azure SQL-Datenbank-Instanzen](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Kann ich eine Datenbank von einer Dienstebene im vCore-basierten Kaufmodell auf eine Dienstebene im DTU-basierten Kaufmodell konvertieren?

Ja. Sie können Ihre Datenbank leicht in ein beliebiges unterstütztes Leistungsziel konvertieren, indem Sie das Azure-Portal, PowerShell, die Azure CLI, T-SQL oder die REST-API verwenden. Weitere Informationen finden Sie unter [Verwalten von Ressourcen für eine einzelne Datenbank in Azure SQL-Datenbank](sql-database-single-database-scale.md) und [Pools für elastische Datenbanken als Hilfe beim Verwalten und Skalieren mehrerer Azure SQL-Datenbank-Instanzen](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu dem vCore-basierten Kaufmodell finden Sie unter [vCore-basiertes Kaufmodell](sql-database-service-tiers-vcore.md).
- Weitere Informationen zu dem DTU-basierten Kaufmodell finden Sie unter [DTU-basiertes Kaufmodell](sql-database-service-tiers-dtu.md).
