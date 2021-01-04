---
title: Ressourceneinschränkungen
titleSuffix: Azure SQL Managed Instance
description: Dieser Artikel bietet eine Übersicht über die Ressourceneinschränkungen für Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: bonova
ms.author: bonova
ms.reviewer: sstein, jovanpop, sachinp
ms.date: 09/14/2020
ms.openlocfilehash: 11c3de703a4b37318b7b99f60d74190fe8ec8610
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077369"
---
# <a name="overview-of-azure-sql-managed-instance-resource-limits"></a>Übersicht über Ressourcenlimits für Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Dieser Artikel bietet eine Übersicht über die technischen Eigenschaften und Ressourcenlimits für Azure SQL Managed Instance und erläutert, wie Sie eine Anforderung zur Erhöhung dieser Grenzwerte erstellen.

> [!NOTE]
> Unterschiede bei den unterstützten Funktionen und T-SQL-Anweisungen sind unter [Funktionsunterschiede](../database/features-comparison.md) und [Unterstützung von T-SQL-Anweisungen](transact-sql-tsql-differences-sql-server.md) zu finden. Allgemeine Unterschiede zwischen Dienstebenen für Azure SQL-Datenbank und SQL Managed Instance finden Sie unter [Vergleich der Dienstebenen](../database/service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="hardware-generation-characteristics"></a>Merkmale der Hardwaregeneration

SQL Managed Instance umfasst Merkmale und Ressourcenlimits, die von der zugrunde liegende Infrastruktur und Architektur abhängen. SQL Managed Instance kann auf zwei Hardwaregenerationen bereitgestellt werden: Gen4 und Gen5. Hardwaregenerationen weisen unterschiedliche Merkmale auf, wie in der folgenden Tabelle beschrieben:

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| **Hardware** | Intel® E5-2673 v3-Prozessoren (Haswell) mit 2,4 GHz, angefügte SSD, virtueller Kern = 1 PP (physischer Kern) | Intel® E5-2673 v4-Prozessoren (Broadwell) mit 2,3 GHz, Intel® SP-8160- und Intel® 8272CL-Prozessoren (Skylake bzw. Cascade Lake) mit 2,5 GHz, schnelle NVMe-SSD, virtueller Kern = 1 LP (Hyperthread) |
| **Anzahl von virtuellen Kernen** | 8, 16, 24 virtuelle Kerne | 4, 8, 16, 24, 32, 40, 64, 80 virtuelle Kerne |
| **Max. Arbeitsspeicher (Verhältnis Arbeitsspeicher/Kerne)** | 7 GB pro V-Kern<br/>Fügen Sie weitere virtuelle Kerne hinzu, um mehr Arbeitsspeicher zu erhalten. | 5,1 GB pro virtuellem Kern<br/>Fügen Sie weitere virtuelle Kerne hinzu, um mehr Arbeitsspeicher zu erhalten. |
| **Max. In-Memory-OLTP-Speicher** | Grenzwert für Instanzen: 1–1,5 GB pro virtuellem Kern| Grenzwert für Instanzen: 0,8–1,65 GB pro virtuellem Kern |
| **Max. reservierter Instanzspeicher** |  Allgemein: 8 TB<br/>Unternehmenskritisch: 1 TB | Allgemein: 8 TB<br/> Unternehmenskritisch: 1 TB, 2 TB oder 4 TB, je nach Anzahl der Kerne |

> [!IMPORTANT]
> - Die Gen4-Hardware wird eingestellt und steht für die neuen Bereitstellungen nicht mehr zur Verfügung. Alle neuen Instanzen von SQL Managed Instance müssen auf Gen5-Hardware bereitgestellt werden.
> - Ziehen Sie in Betracht, [Ihre Instanzen von SQL Managed Instance auf Gen 5-Hardware umzustellen](../database/service-tiers-vcore.md), um von einer größeren Bandbreite an virtuellen Kernen, einer höheren vCore- und Speicherskalierbarkeit, einem beschleunigten Netzwerkbetrieb, einer optimalen E/A-Leistung und minimalen Latenzzeiten zu profitieren.

### <a name="in-memory-oltp-available-space"></a>Verfügbarer Speicherplatz für In-Memory-OLTP 

Die Menge des für In-Memory-OLTP verfügbaren Speicherplatzes auf der Dienstebene [Unternehmenskritisch](../database/service-tier-business-critical.md) hängt von der Anzahl der virtuellen Kerne und der Hardwaraegeneration ab. In der folgenden Tabelle sind die Grenzwerte für den Arbeitsspeicher aufgelistet, der für In-Memory-OLTP-Objekte verwendet werden kann.

| Arbeitsspeicher für In-Memory-OLTP  | **Gen5** | **Gen4** |
| --- | --- | --- |
| 4 virtuelle Kerne  | 3,14 GB | |   
| 8 virtuelle Kerne  | 6,28 GB | 8 GB |
| 16 virtuelle Kerne | 15,77 GB | 20 GB |
| 24 virtuelle Kerne | 25,25 GB | 36 GB |
| 32 virtuelle Kerne | 37,94 GB | |
| 40 virtuelle Kerne | 52,23 GB | |
| 64 virtuelle Kerne | 99,9 GB    | |
| 80 virtuelle Kerne | 131,68 GB| |

## <a name="service-tier-characteristics"></a>Merkmale des Diensttarifs

SQL Managed Instance umfasst zwei Dienstebenen: [Universell](../database/service-tier-general-purpose.md) und [Unternehmenskritisch](../database/service-tier-business-critical.md). Diese Ebenen bieten [verschiedene Funktionen](../database/service-tiers-general-purpose-business-critical.md), die in der folgenden Tabelle beschrieben sind.

> [!Important]
> Die Dienstebene „Unternehmenskritisch“ bietet eine integrierte zusätzliche Kopie der SQL Managed Instance-Instanz (sekundäres Replikat), die für eine schreibgeschützte Workload verwendet werden kann. Wenn Sie Abfragen mit Lese-/Schreibzugriff und Schreibschutz-/Analyse-/Berichtsabfragen voneinander trennen können, erhalten Sie das Doppelte an virtuellen Kernen und Speicher für denselben Preis. Das sekundäre Replikat kann gegenüber der primären Instanz einige Sekunden verzögert sein, daher ist es auf die Auslagerung von Berichts-/Analyse-Workloads ausgelegt, bei denen der aktuelle Datenstatus nicht erforderlich ist. In der Tabelle unten sind die Abfragen, die auf dem sekundären Replikat ausgeführt werden, **Abfragen mit Schreibschutz**.

| **Feature** | **Allgemeiner Zweck** | **Unternehmenskritisch** |
| --- | --- | --- |
| Anzahl der virtuellen Kerne\* | Gen4: 8, 16, 24<br/>Gen5: 4, 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24 <br/> Gen5: 4, 8, 16, 24, 32, 40, 64, 80 <br/>\*Die gleiche Anzahl von virtuellen Kernen ist für Abfragen mit Schreibschutz dediziert. |
| Max. Arbeitsspeicherbelegung | Gen4: 56GB – 168GB (7GB/V-Kern)<br/>Gen5: 20,4 GB – 408 GB (5,1 GB/V-Kern)<br/>Fügen Sie weitere virtuelle Kerne hinzu, um mehr Arbeitsspeicher zu erhalten. | Gen4: 56GB – 168GB (7GB/V-Kern)<br/>Gen5: 20,4 GB – 408 GB (5,1 GB/V-Kern) für Lese-/Schreibabfragen<br/>+ zusätzliche 20,4 GB – 408 GB (5,1 GB/V-Kern) für Abfragen mit Schreibschutz.<br/>Fügen Sie weitere virtuelle Kerne hinzu, um mehr Arbeitsspeicher zu erhalten. |
| Max. Instanzspeichergröße (reserviert) | – 2 TB für 4 virtuelle Kerne (nur Gen5)<br/>– 8 TB für andere Größen | Gen4: 1 TB <br/> Gen5: <br/>– 1 TB für 4, 8, 16 virtuelle Kerne<br/>- 2 TB für 24 virtuelle Kerne<br/>- 4 TB für 32, 40, 64, 80 virtuelle Kerne |
| Max. Datenbankgröße | Bis zur derzeit verfügbaren Instanzgröße (max. 2 TB – 8 TB, abhängig von der Anzahl der virtuellen Kerne). | Bis zur derzeit verfügbaren Instanzgröße (max. 1 TB – 4 TB, abhängig von der Anzahl der virtuellen Kerne). |
| Max. TempDB-Größe | Begrenzt auf 24 GB/V-Kern (96 – 1.920 GB) und die derzeit verfügbare Instanzspeichergröße.<br/>Fügen Sie weitere virtuelle Kerne hinzu, um mehr TempDB-Speicherplatz zu erhalten.<br/> Die Größe der Protokolldatei ist auf 120 GB begrenzt.| Bis zur aktuell verfügbaren Instanzspeichergröße. |
| Max. Anzahl von Datenbanken pro Instanz | 100 Benutzerdatenbanken (es sei denn, der Grenzwert für die Instanzspeichergröße wurde erreicht). | 100 Benutzerdatenbanken (es sei denn, der Grenzwert für die Instanzspeichergröße wurde erreicht). |
| Max. Anzahl von Datenbankdateien pro Instanz | Bis zu 280, außer wenn die Instanzspeichergröße oder der Grenzwert für [Azure Premium Disk-Speicherbelegungsplatz](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files) erreicht wurde. | 32.767 Dateien pro Datenbank, außer wenn der Grenzwert für die Instanzspeichergröße erreicht wurde. |
| Maximale Größe der Datendatei | Begrenzt auf die derzeit verfügbare Instanzspeichergröße (max. 2 TB – 8 TB) und den [Azure Premium Disk-Speicherbelegungsplatz](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files). | Begrenzt auf die derzeit verfügbare Instanzspeichergröße (bis zu 1 TB – 4 TB). |
| Maximale Protokolldateigröße | Begrenzt auf 2 TB und die derzeit verfügbare Instanzspeichergröße. | Begrenzt auf 2 TB und die derzeit verfügbare Instanzspeichergröße. |
| Daten-/Protokoll-IOPS (ungefähr) | Bis zu 30-40.000 IOPS pro Instanz*, 500 – 7.500 pro Datei<br/>\*[Erhöhen Sie die Dateigröße, um den IOPS-Wert zu erhöhen](#file-io-characteristics-in-general-purpose-tier)| 10.000 – 200.000 (4.000 IOPS/virtueller Kern)<br/>Fügen Sie weitere virtuelle Kerne hinzu, um die E/A-Leistung zu verbessern. |
| Grenzwert für den Protokollschreibdurchsatz (pro Instanz) | 3 MB/Sek. pro virtuellem Kern<br/>Max. 120 MB/Sek. pro Instanz<br/>22 – 65 MB/Sek. pro Datenbank<br/>\*[Erhöhen Sie die Dateigröße, um die E/A-Leistung zu verbessern.](#file-io-characteristics-in-general-purpose-tier) | 4 MB/Sek. pro virtuellem Kern<br/>Max. 96 MB/Sek. |
| Datendurchsatz (ungefähr) | 100 bis 250 MB/Sek. pro Datei<br/>\*[Erhöhen Sie die Dateigröße, um die E/A-Leistung zu verbessern.](#file-io-characteristics-in-general-purpose-tier) | Nicht begrenzt. |
| E/A-Speicherlatenz (ungefähr) | 5 – 10 ms | 1 – 2 ms |
| In-Memory-OLTP | Nicht unterstützt | Verfügbar, [Größe hängt von der Anzahl der V-Kerne ab](#in-memory-oltp-available-space) |
| Max. Sitzungen | 30.000 | 30.000 |
| Max. gleichzeitige Worker (Anforderungen) | Gen4: 210 * Anzahl der virtuellen Kerne + 800<br>Gen5: 105 * Anzahl der virtuellen Kerne + 800 | Gen4: 210 * Anzahl der virtuellen Kerne + 800<br>Gen5: 105 * Anzahl der virtuellen Kerne + 800 |
| [Schreibgeschützte Replikate](../database/read-scale-out.md) | 0 | 1 (im Preis inbegriffen) |
| Computeisolation | Gen5:<br/>– unterstützt für 80 virtuelle Kerne<br/>– für andere Größen nicht unterstützt<br/><br/>Gen4 wird eingestellt und deshalb nicht mehr unterstützt.|Gen5:<br/>– unterstützt für 60, 64, 80 virtuelle Kerne<br/>– für andere Größen nicht unterstützt<br/><br/>Gen4 wird eingestellt und deshalb nicht mehr unterstützt.|


Einige weitere Überlegungen: 

- Die **derzeit verfügbare Instanzspeichergröße** ist die Differenz zwischen der reservierten Instanzgröße und dem belegten Speicherplatz.
- Sowohl die Daten- als auch die Protokolldateigröße in den Benutzer- und Systemdatenbanken sind in der Instanzspeichergröße enthalten, die mit dem Grenzwert für die maximale Speichergröße verglichen wird. Ermitteln Sie mithilfe der Systemansicht [sys.master_files](/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql) den von Datenbanken verwendeten Gesamtspeicherplatz. Fehlerprotokolle werden nicht beibehalten und sind nicht in der Größe enthalten. Sicherungen sind nicht in der Speichergröße enthalten.
- Durchsatz und IOPS im Tarif „Universell“ hängen auch von der [Dateigröße](#file-io-characteristics-in-general-purpose-tier) ab, die nicht explizit durch SQL Managed Instance eingeschränkt wird.
  Sie können mithilfe von [Gruppen für ein automatisches Failover](../database/auto-failover-group-configure.md) ein weiteres lesbares Replikat in einer anderen Azure-Region erstellen.
- Der maximale Instanz-IOPS hängt vom Dateilayout und der Workload-Verteilung ab. Wenn Sie z. B. 7 x 1 TB-Dateien mit jeweils maximal 5.000 IOPS und 7 kleine Dateien (kleiner als 128 GB) mit jeweils 500 IOPS erstellen, können Sie 38.500 IOPS pro Instanz (7x5.000+7x500) erhalten, wenn Ihre Workload alle Dateien verwenden kann. Beachten Sie, dass eine bestimmte IOPS-Menge auch für automatische Sicherungen verwendet wird.

Weitere Informationen finden Sie im Artikel zu den [Ressourcenlimits in SQL Managed Instance-Pools](instance-pools-overview.md#resource-limitations).

### <a name="file-io-characteristics-in-general-purpose-tier"></a>Datei-E/A-Merkmale in der Ebene „Universell“

In der Dienstebene „Universell“ erhält jede Datenbankdatei in Abhängigkeit von der Dateigröße dedizierte Mengen an IOPS und Durchsatz. Größere Dateien erhalten mehr IOPS und einen höheren Durchsatz. Die E/A-Merkmale der Datenbankdateien sind in der folgenden Tabelle aufgeführt:

| Dateigröße | >=0 und <=128 GiB | > 128 und <= 512 GiB | >0,5 and <=1 TiB    | >1 und <=2 TiB    | >2 und <=4 TiB | >4 und <=8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| IOPS pro Datei       | 500   | 2300              | 5.000              | 7.500              | 7.500              | 12.500   |
| Durchsatz pro Datei | 100 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s | 480 MiB/s | 

Wenn Sie für bestimmte Datenbankdateien eine hohe E/A-Latenz bemerken oder feststellen, dass der Grenzwert für IOPS/Durchsatz erreicht wird, können Sie die Leistung möglicherweise durch [Vergrößern der Dateigröße](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337) verbessern.

Es gibt auch eine Beschränkung auf Instanzebene für den maximalen Protokollschreibdurchsatz (der 22 MB/s beträgt), sodass Sie den maximalen Dateidurchsatz für die Protokolldatei möglicherweise nicht erreichen, weil der Grenzwert für den Durchsatz auf Instanzebene erreicht wurde.

## <a name="supported-regions"></a>Unterstützte Regionen

SQL Managed Instance-Instanzen können nur in [unterstützten Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all) erstellt werden. Wenn Sie eine SQL Managed Instance-Instanz in einer Region erstellen möchten, die aktuell nicht unterstützt wird, können Sie eine [Supportanfrage über das Azure-Portal senden](../database/quota-increase-request.md).

## <a name="supported-subscription-types"></a>Unterstützte Abonnementtypen

SQL Managed Instance unterstützt aktuell nur die Bereitstellung für folgende Abonnementtypen:

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Nutzungsbasierte Bezahlung](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Clouddienstanbieter (CSP)](/partner-center/csp-documents-and-learning-resources)
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Abonnements mit monatlicher Azure-Gutschrift für Visual Studio-Abonnenten](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Regionale Ressourcenbeschränkungen

> [!Note]
> Um die neuesten Informationen zur regionalen Verfügbarkeit von Abonnements zu erhalten, verwenden Sie zunächst die Option [Region auswählen](https://aka.ms/sqlcapacity).

Unterstützte Abonnementtypen können eine begrenzte Anzahl von Ressourcen pro Region umfassen. Abhängig vom Abonnementtyp gelten für SQL Managed Instance zwei Standardgrenzwerte pro Azure-Region (die bei Bedarf durch das Erstellen einer speziellen [Supportanfrage im Azure-Portal](../database/quota-increase-request.md) erhöht werden können):

- **Subnetzlimit** : Die maximale Anzahl von Subnetzen, wenn Instanzen von SQL Managed Instance in einer einzelnen Region bereitgestellt werden.
- **Grenzwert für virtuelle Kerneinheiten** : Die maximale Anzahl von virtuellen Kerneinheiten, die in einer einzelnen Region über alle Instanzen hinweg bereitgestellt werden können. Ein virtueller Kern „Universell“ verwendet eine einzige virtuelle Kerneinheit, und ein virtueller Kern „Unternehmenskritisch“ verwendet 4 virtuelle Kerneinheiten. Die Gesamtanzahl der Instanzen ist nicht begrenzt, solange sie innerhalb des Limits der virtuellen Kerneinheiten liegt.

> [!Note]
> Diese Limits sind Standardeinstellungen und keine technischen Einschränkungen. Die Limits können bei Bedarf erhöht werden, indem Sie eine spezielle [Supportanfrage im Azure-Portal](../database/quota-increase-request.md) erstellen, falls Sie mehr Instanzen in der aktuellen Region benötigen. Alternativ können Sie auch neue Instanzen von SQL Managed Instance in einer anderen Azure-Region erstellen, ohne Supportanfragen zu senden.

In der folgenden Tabelle werden die **standardmäßigen regionalen Grenzwerte** für unterstützte Abonnementtypen angezeigt (standardmäßige Grenzwerte können mithilfe der unten beschriebenen Supportanfrage erweitert werden):

|Abonnementtyp| Max. Anzahl von SQL Managed Instance-Subnetzen | Max number of vCore units* (Maximale Anzahl von virtuellen Kerneinheiten) |
| :---| :--- | :--- |
|Nutzungsbasierte Bezahlung|3|320|
|CSP |8 (15 in manchen Regionen**)|960 (1440 in manchen Regionen**)|
|Pay-as-you-go Dev/Test|3|320|
|Enterprise Dev/Test|3|320|
|EA|8 (15 in manchen Regionen**)|960 (1440 in manchen Regionen**)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional und MSDN Platforms|2|32|

\* Berücksichtigen Sie bei der Planung von Bereitstellungen, dass die Dienstebene „Unternehmenskritisch“ (Business Critical, BC) viermal (4-mal) mehr v-Kern-Kapazität erfordert als die Dienstebene „Universell“ (General Purpose, GP). Beispiel: 1 virtueller Kern „Universell“ = 1 V-Kern-Einheit, und 1 virtueller Kern „Unternehmenskritisch“ = 4 V-Kern-Einheiten. Um die Nutzungsanalyse hinsichtlich der Standardgrenzwerte zu vereinfachen, fassen Sie die vCore-Einheiten für alle Subnetze in der Region zusammen, in der SQL Managed Instance bereitgestellt wird. Vergleichen Sie die Ergebnisse anschließend mit den Grenzwerten für Instanzeinheiten Ihres Abonnementtyps. Der Grenzwert **Max number of vCore units** (Maximale Anzahl von virtuellen Kerneinheiten) gilt für jedes Abonnement in einer Region. Es gibt keinen Grenzwert pro individuellem Subnetz, außer dass die Summe aller in mehreren Subnetzen bereitgestellten virtuellen Kerne niedriger oder gleich der **maximalen Anzahl von virtuellen Kerneinheiten** sein muss.

\*\* Höhere Grenzwerte für Subnetze und virtuelle Kerne sind in den folgenden Regionen verfügbar: „Australien, Osten“, „USA, Osten“, „USA, Osten 2“, „Europa, Norden“, „USA, Süden-Mitte“, „Asien, Südosten“, „Vereinigtes Königreich, Süden“, „Europa, Westen“, „USA, Westen 2“.

> [!IMPORTANT]
> Wenn das Limit für virtuelle Kerne und das Subnet 0 ist, bedeutet dies, dass das regionale Standardlimit für Ihren Abonnementtyp nicht festgelegt ist. Sie können auch eine Anforderung für eine Kontingenterhöhung für von Abonnementzugriff in einer bestimmten Region nach demselben Verfahren verwenden, indem Sie die erforderlichen Werte für virtuelle Kerne und Subnetze angeben.

## <a name="request-a-quota-increase"></a>Anfordern einer Kontingenterhöhung

Wenn Sie mehr Instanzen in Ihren aktuellen Regionen benötigen, senden Sie eine Supportanfrage zum Erweitern des Kontingents über das Azure-Portal. Weitere Informationen finden Sie unter [Anfordern von Kontingenterhöhungen für Azure SQL-Datenbank](../database/quota-increase-request.md).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu SQL Managed Instance finden Sie unter [Was ist SQL Managed Instance?](sql-managed-instance-paas-overview.md).
- Preisinformationen finden Sie auf der Seite mit den [Preisen für SQL Managed Instance](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Im [Leitfaden für den Schnellstart](instance-create-quickstart.md) erfahren Sie, wie Sie Ihre erste SQL Managed Instance-Instanz erstellen.
