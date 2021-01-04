---
title: Reduzieren der Dienstkosten mithilfe von Azure Advisor
description: Nutzen Sie Azure Advisor, um die Kosten Ihrer Azure-Bereitstellungen zu optimieren.
ms.topic: article
ms.date: 09/27/2020
ms.openlocfilehash: 767c82c818535304552c1c98ff50a252acc0d08a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458519"
---
# <a name="reduce-service-costs-by-using-azure-advisor"></a>Reduzieren der Dienstkosten mithilfe des Azure Advisors

Der Azure Advisor hilft Ihnen beim Optimieren und Senken Ihrer Gesamtausgaben für Azure, indem Ressourcen im Leerlauf bzw. zu gering ausgelastete Ressourcen ermittelt werden.  Kostenempfehlungen finden Sie auf dem Advisor-Dashboard auf der Registerkarte **Kosten**.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimieren der Kosten für virtuelle Computer durch Ändern der Größe oder Herunterfahren von zu gering ausgelasteten Instanzen 

Obwohl bestimmte Anwendungsszenarien zielgerichtet eine geringe Auslastung bewirken, können Sie häufig Kosten sparen, indem Sie Größe und Anzahl Ihrer virtuellen Computer steuern. 

Es wird empfohlenen, die Ressource, die gerade ausgewertet wird, entweder herunterzufahren oder ihre Größe zu ändern.

Gemäß dem erweiterten Auswertungsmodell im Advisor werden virtuelle Computer (Virtual Machines, VMs) heruntergefahren, wenn alle folgenden Aussagen wahr sind: 
- Das 95. Perzentil des Maximums des maximalen Werts für die CPU-Auslastung ist kleiner als 3 %. 
- Die Netzwerkauslastung beträgt innerhalb eines siebentägigen Zeitraums weniger als 2 %.
- Der Druck auf den Arbeitsspeicher liegt unter den Schwellenwerten.

Der Advisor zieht eine Größenänderung für eine VM in Betracht, wenn die aktuelle Last in eine kleinere SKU (innerhalb derselben SKU-Familie) passt. Alternativ muss diese in eine geringere Anzahl von Instanzen passen, sodass Folgendes gilt:
- Die aktuelle Last übersteigt nicht die Auslastungsgrenze von 80 % für Workloads, die nicht benutzerseitig sind. 
- Die Last ist bei benutzerseitigen Workloads nicht größer als 40 %. 

Hier ermittelt der Advisor den Typ der Workload, indem die CPU-Auslastungsmerkmale der Workload analysiert werden.

Der Advisor zeigt die geschätzten Kosteneinsparungen für beide empfohlenen Aktionen an: Größe ändern oder herunterfahren. Bei einer Größenänderung stellt der Advisor aktuelle Informationen sowie Informationen zur Ziel-SKU bereit.

Wenn Sie konsequenter ermitteln möchten, welche VMs zu gering ausgelastet sind, können Sie die Regel für die CPU-Auslastung auf Abonnementbasis anpassen.

## <a name="optimize-spend-for-mariadb-mysql-and-postgresql-servers-by-right-sizing"></a>Optimieren der Ausgaben für MariaDB-, MySQL- und PostgreSQL-Server durch richtiges Dimensionieren 
Der Advisor analysiert Ihre Nutzung und wertet aus, ob Ihre MariaDB-, MySQL- oder PostgreSQL-Datenbankserverressourcen in den vergangenen sieben Tagen über einen längeren Zeitraum hinweg zu gering genutzt wurden. Eine geringe Ressourcenauslastung hat unnötige Ausgaben zur Folge, welche sich ohne signifikante Auswirkungen auf die Leistung korrigieren lassen. Es wird empfohlen, die Computegröße (virtuelle Kerne) zu halbieren, um Ihre Kosten zu senken und Ihre Ressourcen effizient zu verwalten.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Kostensenkung durch die Beseitigung nicht bereitgestellter ExpressRoute-Verbindungen

Advisor ermittelt mit Azure ExpressRoute-Verbindungen, die sich seit mehr als einem Monat im Anbieterstatus **Nicht bereitgestellt** befinden. Er empfiehlt das Löschen der Verbindung, wenn Sie nicht beabsichtigen, die Verbindung mit Ihrem Konnektivitätsanbieter bereitzustellen.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Verringern der Kosten durch Löschen oder Neukonfigurieren von Gateways für virtuelle Netzwerke im Leerlauf

Der Advisor identifiziert Gateways für virtuelle Netzwerke, die sich mehr als 90 Tage im Leerlauf befanden. Da diese Gateways pro Stunde abgerechnet werden, sollten Sie sie neu konfigurieren oder löschen, wenn Sie nicht mehr beabsichtigen, diese zu verwenden. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Erwerben reservierter VM-Instanzen, um gegenüber dem nutzungsbasierten Modell Kosten einzusparen

Der Advisor überprüft Ihre VM-Nutzung der letzten 30 Tage, um zu ermitteln, ob der Erwerb einer Azure-Reservierung für Sie günstiger wäre. Der Advisor zeigt Ihnen die Regionen und Größen, in denen das Einsparungspotenzial am größten ist, sowie die geschätzten Einsparungen durch Kaufreservierungen. Mit Azure-Reservierungen können Sie die Grundkosten für Ihre virtuellen Computer vorab begleichen. Rabatte gelten automatisch für neue oder vorhandene VMs mit der gleichen Größe und Region wie Ihre Reservierungen. Weitere Informationen zu Azure Reserved Virtual Machine Instances finden Sie [hier](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Der Advisor benachrichtigt Sie auch, wenn reservierte Instanzen in den nächsten 30 Tagen ablaufen, und empfiehlt Ihnen, neue reservierte Instanzen zu erwerben, um eine nutzungsbasierte Bezahlung vermeiden.

## <a name="buy-reserved-instances-for-several-resource-types-to-save-over-your-pay-as-you-go-costs"></a>Erwerben reservierter Instanzen für mehrere Ressourcentypen zum Sparen gegenüber der nutzungsbasierten Bezahlung

Der Advisor analysiert die Nutzungsmuster der vergangenen 30 Tage für die folgenden Ressourcen und empfiehlt den Erwerb von reservierter Kapazität, um die Kosten optimieren.

### <a name="azure-cosmos-db-reserved-capacity"></a>Reservierte Kapazität für Azure Cosmos DB
Der Advisor analysiert Ihre Azure Cosmos DB-Nutzungsmuster der vergangenen 30 Tage und empfiehlt den Erwerb von reservierter Kapazität, um die Kosten zu optimieren. Mithilfe von reservierter Kapazität können Sie die Nutzungsstunden für Azure Cosmos DB vorab erwerben und sparen so gegenüber der nutzungsbasierten Bezahlung. Die reservierte Kapazität ist ein Abrechnungsvorteil und gilt automatisch für neue und vorhandene Bereitstellungen. Der Advisor berechnet die geschätzten Einsparungen für einzelne Abonnements anhand des Preises einer dreijährigen Reservierung und anhand der extrapolierten Nutzungsmuster, die in den letzten 30 Tage beobachtet wurden. Es sind auch Empfehlungen für den Kauf von reservierten Kapazitäten verfügbar, die für mehrere Abonnements gelten. Diese können dazu beitragen, noch mehr Kosten zu sparen.

### <a name="sql-database-and-sql-managed-instance-reserved-capacity"></a>Reservierte Kapazität für SQL-Datenbank und SQL Managed Instance
Advisor analysiert die Nutzungsmuster von SQL-Datenbank und SQL Managed Instance, die in den letzten 30 Tagen beobachtet wurden. Anschließend empfiehlt er den Erwerb von reservierten Kapazitäten, die die Kosten optimieren. Mit reservierten Kapazitäten können Sie die Nutzungsstunden von SQL-Datenbanken vorab erwerben und so SQL-Computekosten sparen. Ihre SQL-Lizenz wird separat abgerechnet und durch die Reservierung nicht vergünstigt. Die reservierte Kapazität ist ein Abrechnungsvorteil und gilt automatisch für neue und vorhandene Bereitstellungen. Der Advisor berechnet die geschätzten Einsparungen für einzelne Abonnements anhand des Preises einer dreijährigen Reservierung und anhand der extrapolierten Nutzungsmuster, die in den letzten 30 Tage beobachtet wurden. Es sind auch Empfehlungen für den Kauf von reservierten Kapazitäten verfügbar, die für mehrere Abonnements gelten. Diese können dazu beitragen, noch mehr Kosten zu sparen. Details finden Sie unter [Reservierte Kapazität für SQL-Datenbank und SQL Managed Instance](../azure-sql/database/reserved-capacity-overview.md).

### <a name="app-service-stamp-fee-reserved-capacity"></a>Reservierte Kapazität für die App Service-Stempelgebühr
Der Advisor analysiert die Stempelgebühr-Nutzungsmuster für Ihre isolierte Azure App Service-Umgebung für die vergangenen 30 Tage und empfiehlt den Erwerb von reservierter Kapazität, um die Kosten zu optimieren. Mit reservierter Kapazität können Sie die Nutzungsstunden von Stempelgebühren für eine isolierte Umgebung vorab erwerben und sparen so gegenüber der nutzungsbasierten Bezahlung. Beachten Sie, dass die reservierte Kapazität nur für die Stempelgebühr und nicht für App Service-Instanzen gilt. Die reservierte Kapazität ist ein Abrechnungsvorteil und gilt automatisch für neue und vorhandene Bereitstellungen. Der Advisor berechnet die geschätzten Einsparungen für einzelne Abonnements anhand des Preises einer dreijährigen Reservierung und der Nutzungsmuster der letzten 30 Tage.

### <a name="blob-storage-reserved-capacity"></a>Reservierte Kapazität für Blob Storage
Der Advisor analysiert die Nutzung Ihres Azure-Blob- und Azure Data Lake-Speichers innerhalb der letzten 30 Tage. Anschließend berechnet er den Erwerb von reservierten Kapazitäten, die die Kosten optimieren. Mithilfe von reservierter Kapazität können Sie Nutzungsstunden vorab erwerben und so gegenüber Ihren aktuellen Kosten für bedarfsabhängige Nutzung sparen. Reservierte Kapazität für Blob Storage ist nur für Daten verwendbar, die in Azure Blob Storage- (Universell V2) und Azure Data Lake Storage Gen2-Konten gespeichert sind. Die reservierte Kapazität ist ein Abrechnungsvorteil und gilt automatisch für neue und vorhandene Bereitstellungen. Der Advisor berechnet die geschätzten Einsparungen für einzelne Abonnements anhand des Preises einer dreijährigen Reservierung und der Nutzungsmuster, die in den letzten 30 Tage beobachtet wurden. Es sind auch Empfehlungen für den Kauf von reservierten Kapazitäten verfügbar, die für mehrere Abonnements gelten. Diese können dazu beitragen, noch mehr Kosten zu sparen.

### <a name="mariadb-mysql-and-postgresql-reserved-capacity"></a>Reservierte Kapazität für MariaDB, MySQL und PostgreSQL
Der Advisor analysiert Ihre Nutzungsmuster für Azure Database for MariaDB, Azure Database for MySQL und Azure Database for PostgreSQL innerhalb der letzten 30 Tage. Anschließend empfiehlt er den Erwerb von reservierten Kapazitäten, die die Kosten optimieren. Mithilfe von reservierter Kapazität können Sie vorab Nutzungsstunden für MariaDB,MySQL und PostgreSQL erwerben und so bei Ihren aktuellen Kosten sparen. Die reservierte Kapazität ist ein Abrechnungsvorteil und gilt automatisch für neue und vorhandene Bereitstellungen. Der Advisor berechnet die geschätzten Einsparungen für einzelne Abonnements anhand des Preises einer dreijährigen Reservierung und der Nutzungsmuster, die in den letzten 30 Tage beobachtet wurden. Es sind auch Empfehlungen für den Kauf von reservierten Kapazitäten verfügbar, die für mehrere Abonnements gelten. Diese können dazu beitragen, noch mehr Kosten zu sparen.

### <a name="azure-synapse-analytics-reserved-capacity"></a>Reservierte Azure Synapse Analytics-Kapazität
Der Advisor analysiert Ihre Azure Synapse Analytics-Nutzungsmuster der vergangenen 30 Tage und empfiehlt den Erwerb von reservierter Kapazität, um die Kosten zu optimieren. Mithilfe von reservierter Kapazität können Sie die Nutzungsstunden von Synapse Analytics vorab erwerben und Einsparungen im Vergleich zur bedarfsabhängigen Nutzung erzielen. Die reservierte Kapazität ist ein Abrechnungsvorteil und gilt automatisch für neue und vorhandene Bereitstellungen. Der Advisor berechnet die geschätzten Einsparungen für einzelne Abonnements anhand des Preises einer dreijährigen Reservierung und der Nutzungsmuster, die in den letzten 30 Tage beobachtet wurden. Es sind auch Empfehlungen für den Kauf von reservierten Kapazitäten verfügbar, die für mehrere Abonnements gelten. Diese können dazu beitragen, noch mehr Kosten zu sparen.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Löschen nicht zugeordneter öffentlicher IP-Adressen zum Einsparen von Kosten

Der Advisor ermittelt öffentliche IP-Adressen, die keiner Azure-Ressource wie einem Lastenausgleich oder einer VM zugeordnet sind. Für diese öffentliche IP-Adressen fällt eine Schutzgebühr an. Wenn Sie nicht vorhaben, sie zu nutzen, können Sie Kosten sparen, indem Sie sie löschen.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Löschen fehlerhafter Azure Data Factory-Pipelines

Der Advisor ermittelt Azure Data Factory-Pipelines, die wiederholt fehlschlagen. Er empfiehlt, die Probleme zu beheben oder die Pipelines zu löschen, wenn Sie diese nicht benötigen. Für diese Pipelines fallen auch dann Kosten an, wenn sie sich in einem Fehlerzustand befinden und nicht genutzt werden können.

## <a name="use-standard-snapshots-for-managed-disks"></a>Verwenden von Standardmomentaufnahmen für verwaltete Datenträger
Es empfiehlt sich, Momentaufnahmen unabhängig vom Speichertyp des übergeordneten Datenträgers in Storage Standard zu speichern, um 60 % der Kosten zu sparen. Dies ist die Standardoption für Momentaufnahmen von verwalteten Datenträgern. Der Advisor erkennt in Storage Premium gespeicherte Momentaufnahmen und empfiehlt, Ihre Momentaufnahme von Storage Premium zu Storage Standard zu migrieren. Weitere Informationen zu den Preisen für verwaltete Datenträger finden Sie [hier](https://aka.ms/aa_manageddisksnapshot_learnmore).

## <a name="use-lifecycle-management"></a>Verwenden der Lebenszyklusverwaltung
Mithilfe von Informationen zur Objektanzahl, der Gesamtgröße und den Transaktionen Ihrer Azure Blob Storage-Instanz ermittelt der Advisor, ob sich die Aktivierung der Lebenszyklusverwaltung empfiehlt, um Daten in einem oder zwischen mehreren Ihrer Speicherkonten dynamisch zu verschieben. Sie werden aufgefordert, Verwaltungsregeln für den Lebenszyklus zu erstellen, um Ihre Daten zur Optimierung Ihrer Speicherkosten automatisch in die kalte Speicherebene oder in die Archivebene zu verschieben. Dies hat keine Auswirkungen auf die Anwendungskompatibilität, da die Daten weiterhin in Azure Blob Storage vorhanden sind.

## <a name="create-an-ephemeral-os-disk-recommendation"></a>Erstellen einer Empfehlung zu kurzlebigen Betriebssystemdatenträgern
[Kurzlebige Betriebssystemdatenträger](../virtual-machines/ephemeral-os-disks.md) ermöglichen Ihnen Folgendes: 
- Sparen der Speicherkosten für Betriebssystemdatenträger 
- Reduzieren der Lese-/Schreibwartezeit auf dem Betriebssystemdatenträger 
- Schnelleres VM-Reimaging durch Zurücksetzen des Betriebssystems (und des temporären Datenträgers) in den ursprünglichen Zustand

Es empfiehlt sich, für kurzlebige IaaS-VMs oder VMs mit zustandslosen Workloads kurzlebige Betriebssystemdatenträger zu verwenden. Der Advisor enthält Empfehlungen für Ressourcen, die gut mit kurzlebigen Betriebssystemdatenträgern funktionieren.

## <a name="reduce-azure-data-explorer-table-cache-period-policy-for-cluster-cost-optimization-preview"></a>Verringern des Cachezeitraums von Azure Data Explorer-Tabellen (Richtlinie) zur Optimierung der Clusterkosten (Vorschau)
Advisor identifiziert Ressourcen, bei denen durch die Reduzierung der Tabellencacherichtlinie Azure Data Explorer-Clusterknoten mit geringer CPU-Auslastung, wenig Arbeitsspeicher und einer Konfiguration mit großem Cache freigegeben werden.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Zugreifen auf Kostenempfehlungen in Azure Advisor

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Suchen Sie auf einer beliebigen Seite nach dem Eintrag [**Advisor**](https://aka.ms/azureadvisordashboard), und wählen Sie ihn aus.

1. Wählen Sie auf dem **Advisor**-Dashboard die Registerkarte **Kosten** aus.

## <a name="next-steps"></a>Nächste Schritte

Hier finden Sie weitere Informationen zu Empfehlungen des Advisor:
* [Einführung in Advisor](advisor-overview.md)
* [Advisor-Bewertung](azure-advisor-score.md)
* [Erste Schritte mit Advisor](advisor-get-started.md)
* [Verbessern der Leistung von Azure-Anwendungen mit dem Azure Advisor](advisor-performance-recommendations.md)
* [Verbessern der Verfügbarkeit Ihrer Anwendung mit Azure Advisor](advisor-high-availability-recommendations.md)
* [Erhöhen der Sicherheit von Ressourcen mit dem Azure Advisor](advisor-security-recommendations.md)
* [Sicherstellen des optimalen Betriebs mit dem Azure Advisor](advisor-operational-excellence-recommendations.md)