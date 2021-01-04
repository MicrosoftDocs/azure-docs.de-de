---
title: Leistungsrichtlinien für SQL Server in Azure | Microsoft-Dokumentation
description: Dieses Thema enthält Richtlinien zur Optimierung der Leistung von SQL Server auf Microsoft Azure Virtual Machines.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.subservice: performance
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/09/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 2b4e8d980ee2b5c69687fc7ad8975e26fe38071a
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97360117"
---
# <a name="performance-guidelines-for-sql-server-on-azure-virtual-machines"></a>Leistungsrichtlinien für SQL Server auf Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Dieser Artikel enthält Informationen zur Optimierung der Leistung von SQL Server auf Microsoft Azure Virtual Machines.

## <a name="overview"></a>Übersicht

Es empfiehlt sich, beim Ausführen von SQL Server auf Azure Virtual Machines weiterhin die gleichen Optionen zur Optimierung der Datenbankleistung zu verwenden, die für SQL Server in lokalen Serverumgebungen gelten. Die Leistung einer relationalen Datenbank in einer öffentlichen Cloud hängt jedoch von vielen Faktoren ab, z. B. der Größe eines virtuellen Computers und der Konfiguration der Datenträger für Daten.

Für [im Azure-Portal bereitgestellte SQL Server-Images](sql-vm-create-portal-quickstart.md) werden die allgemeinen [bewährten Methoden für die Speicherkonfiguration](storage-configuration.md) berücksichtigt. Nach der Bereitstellung empfiehlt es sich gegebenenfalls, weitere der in diesem Artikel behandelten Optimierungen anzuwenden. Orientieren Sie sich dabei an Ihrer Workload, und testen Sie die gewählte Option.

> [!TIP]
> In der Regel kommt es zu einem Kompromiss zwischen einer Kostenoptimierung und einer Leistungsoptimierung. Dieser Artikel konzentriert sich auf die *optimale* Leistung von SQL Server auf Azure Virtual Machines. Wenn Ihre Workload weniger anspruchsvoll ist, sind möglicherweise nicht alle unten aufgeführten Optimierungen erforderlich. Berücksichtigen Sie bei der Evaluierung dieser Empfehlungen Ihre Leistungsanforderungen und -kosten sowie Ihre Workloadmuster.

## <a name="quick-checklist"></a>Kurze Checkliste

Im folgenden finden eine kurze Checkliste für die optimale Leistung von SQL Server auf Azure Virtual Machines:

| Bereich | Optimierungen |
| --- | --- |
| [Größe des virtuellen Computers](#vm-size-guidance) | - Verwenden Sie VM-Größen mit vier oder mehr vCPUs, z. B. [Standard_M8-4ms](/azure/virtual-machines/m-series), [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series) und [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) oder höher. <br/><br/> - Verwenden Sie VM-Größen mit [Arbeitsspeicheroptimierung](../../../virtual-machines/sizes-memory.md), um die bestmögliche Leistung für SQL Server-Workloads zu erzielen. <br/><br/> - Die Serien [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md), [M](/azure/virtual-machines/m-series) und [Mv2](../../../virtual-machines/mv2-series.md) führen zu dem optimalen Verhältnis von Arbeitsspeicher zu virtuellen Kernen, das für OLTP-Workloads benötigt wird. Beide VMs der M-Serie bieten das beste Verhältnis von Arbeitsspeicher zu virtuellen Kernen für unternehmenskritische Workloads und sind zudem ideal für Data Warehouse-Workloads geeignet. <br/><br/> - Für unternehmenskritische und Data Warehouse-Workloads ist unter Umständen ein höheres Verhältnis von Arbeitsspeicher zu virtuellen Kernen erforderlich. <br/><br/> - Nutzen Sie die Azure Virtual Machines-Marketplace-Images, da die SQL Server-Einstellungen und Speicheroptionen für eine optimale SQL Server-Leistung konfiguriert sind. <br/><br/> - Erfassen Sie die Leistungsmerkmale der Zielworkload, und nutzen Sie sie zum Ermitteln der passenden VM-Größe für Ihr Unternehmen.|
| [Storage](#storage-guidance) | Ausführliche Informationen zum Testen der SQL Server-Leistung auf Azure Virtual Machines mit TPC-E- und TPC_C-Benchmarks finden Sie im Blog [Optimieren der OLTP-Leistung](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). <br/><br/> – Verwenden Sie [SSD Premium](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) für die besten Preis-/Leistungsvorteile. Konfigurieren Sie [schreibgeschützten Cache](../../../virtual-machines/premium-storage-performance.md#disk-caching) für Datendateien und keinen Cache für die Protokolldatei. <br/><br/> - Verwenden Sie [Ultra Disks](../../../virtual-machines/disks-types.md#ultra-disk), wenn für die Workload eine Speicherlatenz von weniger als 1 ms erforderlich ist. Weitere Informationen finden Sie im Artikeln zum [Migrieren von Protokolldatenträgern zu Ultra-Datenträgern](storage-migrate-to-ultradisk.md). <br/><br/> – Erfassen Sie die Speicherlatenzanforderungen für SQL Server-Daten-, Protokoll- und Temp DB-Dateien, indem Sie [die Anwendung überwachen](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist), bevor Sie den Datenträgertyp auswählen. Wenn Speicherlatenzzeiten von weniger als einer Millisekunde erforderlich sind, sollten Sie Ultra Disks verwenden (ansonsten SSD Premium). Wenn niedrige Latenzen nur für die Protokolldatei und nicht für Datendateien erforderlich sind, [stellen Sie die Ultra Disk](../../../virtual-machines/disks-enable-ultra-ssd.md) auf den erforderlichen IOPS- und Durchsatzebenen nur für die Protokolldatei bereit. <br/><br/>  – Standardspeicher wird nur für Entwicklungs- und Testzwecke oder für Sicherungsdateien empfohlen und sollte nicht für Produktionsworkloads verwendet werden. <br/><br/> - Speichern Sie das [Speicherkonto](../../../storage/common/storage-account-create.md) und die SQL Server-VM in derselben Region.<br/><br/> – Deaktivieren Sie [georedundanten Azure-Speicher](../../../storage/common/storage-redundancy.md) (Georeplikation) für das Speicherkonto.  |
| [Datenträger](#disks-guidance) | – Verwenden Sie mindestens 2 [SSD Premium-Datenträger](../../../virtual-machines/disks-types.md#premium-ssd) (1 Datenträger für Protokolldatei und 1 Datenträger für Datendateien). <br/><br/> - Aktivieren Sie für Workloads, die E/A-Latenzen von weniger als 1 ms erfordern, die Schreibbeschleunigung für die M-Serie, und verwenden Sie SSD Ultra-Datenträger für die Es- und DS-Serie. <br/><br/> – Aktivieren Sie [schreibgeschützte Zwischenspeicherung](../../../virtual-machines/premium-storage-performance.md#disk-caching) für die Datenträger, auf denen die Datendateien gehostet werden.<br/><br/> – Fügen Sie 20 % mehr Premium-IOPS-/-Durchsatzkapazität hinzu, als Ihre Workload erfordert, wenn Sie [den Speicher für SQL Server-Daten, -Protokolle und TempDB-Dateien konfigurieren](storage-configuration.md). <br/><br/> - Vermeiden Sie die Verwendung von Betriebssystem- oder temporären Datenträgern für die Datenbankspeicherung oder Protokollierung.<br/><br/> - Aktivieren Sie kein Caching auf Datenträgern, auf denen die Protokolldatei gehostet wird.  **Wichtig**: Beenden Sie den SQL Server-Dienst, wenn Sie die Cacheeinstellungen für einen Azure Virtual Machines-Datenträger ändern.<br/><br/> – Erstellen Sie ein Stripeset mehrerer Azure-Datenträger für Daten, um einen höheren Speicherdurchsatz zu erzielen.<br/><br/> - Formatieren Sie mit dokumentierten Zuordnungsgrößen. <br/><br/> - Legen Sie die TempDB für geschäftskritische SQL Server-Workloads auf dem lokalen SSD-Laufwerk `D:\` ab (nach dem Auswählen der richtigen VM-Größe). Wenn Sie die VM im Azure Portal oder mit Azure-Schnellstartvorlagen erstellen und [Temp DB auf dem lokalen Datenträger gespeichert wird](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583), sind keine weiteren Maßnahmen erforderlich. In allen anderen Fällen befolgen Sie die Schritte im Blog zum [Verwenden von SSDs zum Speichern von TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-TempDB-and-buffer-pool-extensions/), um Fehler nach Neustarts zu vermeiden. Wenn die Kapazität des lokalen Laufwerks für die Größe von Temp DB nicht ausreicht, platzieren Sie Temp DB in einem Speicherpool als [Stripeset](../../../virtual-machines/premium-storage-performance.md) auf SSD Premium-Datenträgern mit [schreibgeschützter Zwischenspeicherung](../../../virtual-machines/premium-storage-performance.md#disk-caching). |
| [E/A](#io-guidance) |- Aktivieren Sie die Datenbankseitenkomprimierung.<br/><br/> - Aktivieren Sie die sofortige Dateiinitialisierung für Datendateien.<br/><br/> - Begrenzen Sie die automatische Vergrößerung der Datenbank.<br/><br/> - Deaktivieren Sie die automatische Verkleinerung der Datenbank.<br/><br/> - Verschieben Sie alle Datenbanken, einschließlich der Systemdatenbanken, auf Datenträger für Daten.<br/><br/> - Verschieben Sie die Verzeichnisse für das SQL Server-Fehlerprotokoll und die Ablaufverfolgungsdateien auf die Datenträger für Daten.<br/><br/> – Konfigurieren Sie standardmäßige Dateispeicherorte für Sicherungen und Datenbanken.<br/><br/> - [Aktivieren Sie gesperrte Seiten im Arbeitsspeicher](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).<br/><br/> - Evaluieren Sie die [aktuellen kumulativen Updates](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server) für die installierte Version von SQL Server, und wenden Sie sie an. |
| [Featurespezifisch](#feature-specific-guidance) | – Sichern Sie direkt in Azure Blob Storage.<br/><br/>– Verwenden Sie [Dateimomentaufnahmen-Sicherungen](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) für Datenbanken, die größer als 12 TB sind. <br/><br/>– Verwenden Sie mehrere Temp DB-Dateien, 1 Datei pro Kern, bis zu 8 Dateien.<br/><br/>– Legen Sie den maximalen Arbeitsspeicher des Servers auf 90 % oder bis zu 50 GB für das Betriebssystem verbleibend fest. <br/><br/>– Aktivieren Sie Soft-NUMA. |


<br/>
Weitere Informationen dazu, *wie* und *warum* diese Optimierungen vorgenommen werden, finden Sie in den Details und den Leitfäden in den folgenden Abschnitten.
<br/><br/>

## <a name="getting-started"></a>Erste Schritte

Wenn Sie eine neue SQL Server-Instanz auf einer Azure-VM erstellen und kein aktuelles Quellsystem migrieren, sollten Sie Ihre neue SQL Server-VM basierend auf Ihren Anbieteranforderungen erstellen.  Die Anbieteranforderungen für eine SQL Server-VM sind mit den Anforderungen für die lokale Bereitstellung identisch. 

Wenn Sie eine neue SQL Server-VM mit einer neuen Anwendung für die Cloud erstellen, können Sie die Größe Ihrer SQL Server-VM leicht anpassen, falls sich Ihre Daten- und Nutzungsanforderungen ändern sollten.
Starten Sie die Entwicklungsumgebungen mit der kleineren D-Serie, B-Serie oder Av2-Serie, und erweitern Sie Ihre Umgebung dann im Laufe der Zeit. 

Die empfohlene Mindestspezifikation für eine OLTP-Produktionsumgebung umfasst 4 virtuelle Kerne, 32 GB Arbeitsspeicher und ein Verhältnis von Arbeitsspeicher zu virtuellen Kernen (Kurzbezeichnung: Arbeitsspeicher/Kern-Verhältnis) von „8“. Beginnen Sie bei neuen Umgebungen mit Computern mit vier virtuellen Kernen, und skalieren Sie dann auf 8, 16, 32 oder mehr virtuelle Kerne hoch, wenn sich Ihre Daten- und Computeanforderungen ändern. Streben Sie für den OLTP-Durchsatz die Verwendung von SQL Server-VMs an, die über 5.000 IOPS pro virtuellem Kern verfügen. 

Verwenden Sie die Marketplace-Images für SQL Server-VMs mit der Speicherkonfiguration im Portal. Dies vereinfacht die richtige Erstellung der Speicherpools, die zum Erzielen der erforderlichen Größe, IOPS-Werte und Durchsatzwerte für Ihre Workloads benötigt werden. Es ist wichtig, SQL Server-VMs auszuwählen, für die Storage Premium und die Storage Premium-Zwischenspeicherung unterstützt werden. Weitere Informationen finden Sie im Abschnitt zum Thema [Speicher](#storage-guidance). 

Für SQL Server Data Warehouse und unternehmenskritische Umgebungen muss der Wert „8“ für das Verhältnis von Arbeitsspeicher zu virtuellen Kernen häufig erhöht werden. Für mittelgroße Umgebungen ist es ratsam, ein Arbeitsspeicher/Kern-Verhältnis von „16“ auszuwählen, während der Wert für größere Data Warehouse-Umgebungen „32“ betragen sollte. 

SQL Server Data Warehouse-Umgebungen profitieren häufig von der Parallelverarbeitung größerer Computer. Aus diesem Grund sind die M-Serie und die Mv2-Serie gute Optionen für größere Data Warehouse-Umgebungen.

## <a name="vm-size-guidance"></a>Leitfaden für die Größe virtueller Computer

Verwenden Sie die vCPU- und Arbeitsspeicherkonfiguration Ihres Quellcomputers als Baseline für die Migration einer aktuellen lokalen SQL Server-Datenbank zu SQL Server auf Azure-VMs. Verwenden Sie Ihre eigene Core-Lizenz für Azure, um vom [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/) zu profitieren und SQL Server-Lizenzierungskosten zu sparen.

**Microsoft empfiehlt ein Arbeitsspeicher/Kern-Verhältnis von „8“ als Ausgangspunkt für SQL Server-Workloads in der Produktion.** Niedrigere Werte für dieses Verhältnis sind für Workloads außerhalb der Produktionsumgebung geeignet. 

Wählen Sie eine VM-Größe vom Typ [Arbeitsspeicheroptimiert](../../../virtual-machines/sizes-memory.md), [Universell](../../../virtual-machines/sizes-general.md), [Datenspeicheroptimiert](../../../virtual-machines/sizes-storage.md) oder [Eingeschränkte virtuelle Kerne](../../../virtual-machines/constrained-vcpu.md) aus, mit der je nach Ihrer Workload (OLTP oder Data Warehouse) die optimale SQL Server-Leistung erzielt werden kann. 

### <a name="memory-optimized"></a>Arbeitsspeicheroptimiert

Die [VM-Größen mit Arbeitsspeicheroptimierung](../../../virtual-machines/sizes-memory.md) sind ein primäres Ziel für SQL Server-VMs und werden von Microsoft empfohlen. Virtuelle Computer mit Arbeitsspeicheroptimierung verfügen über bessere Arbeitsspeicher/CPU-Verhältnisse und ermöglichen die Zwischenspeicherung im mittleren und hohen Bereich. 

#### <a name="m-and-mv2-series"></a>M- und Mv2-Serie

Die [M-Serie](/azure/virtual-machines/m-series) verfügt über eine Anzahl virtueller Kerne und Arbeitsspeichermenge für einige der umfangreichsten SQL Server-Workloads.  

Die [Mv2-Serie](../../../virtual-machines/mv2-series.md) verfügt über die höchste Anzahl virtueller Kerne und Arbeitsspeichermenge und ist für unternehmenskritische und Data Warehouse-Workloads zu empfehlen. Bei Instanzen der Mv2-Serie handelt es sich um arbeitsspeicheroptimierte VM-Größen zur Bereitstellung einer unvergleichlichen Computeleistung, um große In-Memory-Datenbanken und Workloads zu unterstützen. Sie verfügen über ein hohes Arbeitsspeicher/CPU-Verhältnis, das perfekt für relationale Datenbankserver, große Caches und In-Memory-Analysen geeignet ist.

Bei [Standard_M64ms](/azure/virtual-machines/m-series) beträgt der Wert für das Arbeitsspeicher/Kern-Verhältnis beispielsweise „28“.

Beispiele für einige Features der M- und Mv2-Serie, die eine hohe SQL Server-Leistung ermöglichen, sind die Unterstützung von [Storage Premium](../../../virtual-machines/premium-storage-performance.md) und [Storage Premium-Zwischenspeicherung](../../../virtual-machines/premium-storage-performance.md#disk-caching), die Unterstützung von [Ultra-Datenträgern](../../../virtual-machines/disks-enable-ultra-ssd.md) und [Schreibbeschleunigung](../../../virtual-machines/how-to-enable-write-accelerator.md).

#### <a name="edsv4-series"></a>Edsv4-Serie

Die [Edsv4-Serie](../../../virtual-machines/edv4-edsv4-series.md) ist auf arbeitsspeicherintensive Anwendungen ausgelegt. Diese VMs verfügen über hohe lokale SSD-Speicherkapazität, einen hohen IOPS-Wert für lokale Datenträger, bis zu 504 GiB RAM und eine verbesserte Computeleistung im Vergleich zu den vorherigen Ev3-/Esv3-Größen bei Gen2-VMs. Das Arbeitsspeicher/Kern-Verhältnis liegt für diese virtuellen Computer fast immer bei „8“. Dies ist der ideale Wert für SQL Server-Standardworkloads. 

Diese VM-Serie eignet sich ideal für arbeitsspeicherintensive Unternehmensanwendungen und Anwendungen, die von lokalem Hochgeschwindigkeitsspeicher mit geringer Latenz profitieren.

Für die virtuellen Computer der Edsv4-Serie werden [Storage Premium](../../../virtual-machines/premium-storage-performance.md) und die [Storage Premium-Zwischenspeicherung](../../../virtual-machines/premium-storage-performance.md#disk-caching) unterstützt.

#### <a name="dsv2-series-11-15"></a>DSv2-Serie 11-15

Die [DSv2-Serie 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) weist die gleichen Arbeitsspeicher- und Datenträgerkonfigurationen wie die vorherige D-Serie auf. Diese Serie verfügt für alle virtuellen Computer über ein einheitliches Arbeitsspeicher/CPU-Verhältnis von „7“. 

Für die [DSv2-Serie 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) werden [Storage Premium](../../../virtual-machines/premium-storage-performance.md) und die [Storage Premium-Zwischenspeicherung](../../../virtual-machines/premium-storage-performance.md#disk-caching) unterstützt. Dies wird dringend empfohlen, um die optimale Leistung zu erzielen.

### <a name="general-purpose"></a>Universell

Die [VM-Größen vom Typ „Universell“](../../../virtual-machines/sizes-general.md) sind darauf ausgelegt, ausgeglichene Arbeitsspeicher/Kern-Verhältnisse für weniger umfangreiche Workloads auf Einstiegsebene bereitzustellen, z. B. Entwicklung und Tests, Webserver und kleinere Datenbankserver. 

Aufgrund der niedrigeren Arbeitsspeicher/Kern-Verhältnisse bei den VMs vom Typ „Universell“ ist es wichtig, die arbeitsspeicherbasierten Leistungsindikatoren sorgfältig zu überwachen. So kann sichergestellt werden, dass SQL Server den benötigten Puffercache-Arbeitsspeicher erhält. Weitere Informationen finden Sie im Abschnitt zur [Baseline für die Arbeitsspeicherleistung](#memory). 

Da für Produktionsworkloads ein anfängliches Arbeitsspeicher/Kern-Verhältnis von „8“ empfohlen wird, lautet die Mindestempfehlung für die Konfiguration einer VM vom Typ „Universell“ mit SQL Server wie folgt: 4 vCPUs und 32 GB Arbeitsspeicher. 

#### <a name="ddsv4-series"></a>Ddsv4-Serie

Die [Ddsv4-Serie](../../../virtual-machines/ddv4-ddsv4-series.md) bietet eine ausgewogene Kombination in Bezug auf vCPU, Arbeitsspeicher und temporären Datenträger, verfügt aber über eine eingeschränkte Arbeitsspeicher/Kern-Unterstützung. 

Die Ddsv4-VMs ermöglichen eine geringere Latenz und lokale Speichervorgänge mit höherer Geschwindigkeit.

Diese Computer eignen sich ideal für parallele SQL- und App-Bereitstellungen, für die ein schneller Zugriff auf den temporären Speicher und die relationalen Datenbanken verschiedener Abteilungen benötigt wird. Für alle virtuellen Computer dieser Serie beträgt das Arbeitsspeicher/Kern-Verhältnis standardmäßig „4“. 

Aus diesem Grund empfehlen wir Ihnen, die VM „D8ds_v4“ als Start-VM dieser Serie zu nutzen. Sie verfügt über acht virtuelle Kerne und 32 GB Arbeitsspeicher. Der größte Computer ist der „D64ds_v4“ mit 64 virtuellen Kernen und 256 GB Arbeitsspeicher.

Für die virtuellen Computer der [Ddsv4-Serie](../../../virtual-machines/ddv4-ddsv4-series.md) werden [Storage Premium](../../../virtual-machines/premium-storage-performance.md) und die [Storage Premium-Zwischenspeicherung](../../../virtual-machines/premium-storage-performance.md#disk-caching) unterstützt.

> [!NOTE]
> Die [Ddsv4-Serie](../../../virtual-machines/ddv4-ddsv4-series.md) verfügt nicht über das Arbeitsspeicher/Kern-Verhältnis von „8“, das für SQL Server-Workloads empfohlen wird. Aus diesem Grund ist es ratsam, diese virtuellen Computer nur für kleinere Anwendungen und Entwicklungsworkloads zu verwenden.

#### <a name="b-series"></a>B-Serie

Die Größen der [burstfähigen VMs der B-Serie](../../../virtual-machines/sizes-b-series-burstable.md) sind ideal für Workloads geeignet, für die keine einheitliche Leistung erforderlich ist, z. B. Proof of Concept und sehr kleine Anwendungs- und Entwicklungsserver. 

Die meisten [burstfähigen VMs der B-Serie](../../../virtual-machines/sizes-b-series-burstable.md) verfügen über ein Arbeitsspeicher/Kern-Verhältnis von „4“. Der größte Computer ist hierbei der [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) mit 20 virtuellen Kernen und 80 GB Arbeitsspeicher.

Diese Serie ist einzigartig, weil für die Apps während der Geschäftszeiten **Bursts** erfolgen können, wobei der Umfang der Burstfähigkeit je nach Computergröße variiert. 

Wenn das Guthaben erschöpft ist, wird die VM auf die Baseline-Computerleistung zurückgestuft.

Der Vorteil der B-Serie sind die Computeeinsparungen, die Sie im Vergleich zu den anderen VM-Größen anderer Serien erzielen können. Dies gilt besonders, wenn Sie die Verarbeitungsleistung über den Tag verteilt nicht sehr häufig benötigen.

Bei dieser Serie wird [Storage Premium](../../../virtual-machines/premium-storage-performance.md) unterstützt, aber **nicht** [Storage Premium-Zwischenspeicherung](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE] 
> Die [burstfähige B-Serie](../../../virtual-machines/sizes-b-series-burstable.md) verfügt nicht über das Arbeitsspeicher/Kern-Verhältnis von „8“, das für SQL Server-Workloads empfohlen wird. Aus diesem Grund ist es ratsam, diese virtuellen Computer nur für kleinere Anwendungen, Webserver und Entwicklungsworkloads zu verwenden.

#### <a name="av2-series"></a>Av2-Serie

Die VMs der [Av2-Serie](../../../virtual-machines/av2-series.md) eignen sich am besten für Workloads auf Einstiegsebene, z. B. Entwicklung und Tests, Webserver mit wenig Datenverkehr, kleinere bis mittlere App-Datenbanken und Proof of Concept-Vorgänge.

Nur [Standard_A2m_v2](../../../virtual-machines/av2-series.md) (2 virtuelle Kerne und 16 GB Arbeitsspeicher), [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 virtuelle Kerne und 32 GB Arbeitsspeicher) und [Standard_A8m_v2](../../../virtual-machines/av2-series.md) (8 virtuelle Kerne und 64 GB Arbeitsspeicher) verfügen über ein gutes Arbeitsspeicher/Kern-Verhältnis von „8“ für diese drei virtuellen Computer der obersten Ebene. 

Diese virtuellen Computer sind eine gute Option, wenn kleinere SQL Server-Computer für Entwicklungs- und Testzwecke benötigt werden. 

[Standard_A8m_v2](../../../virtual-machines/av2-series.md) mit acht virtuellen Kernen kann ebenfalls eine gute Option für kleine Anwendungen und Webserver sein.

> [!NOTE] 
> Für die Av2-Serie wird Storage Premium nicht unterstützt. Sie ist daher auch dann nicht für SQL Server-Produktionsworkloads zu empfehlen, wenn die virtuellen Computer mit einem Arbeitsspeicher/Kern-Verhältnis von „8“ verwendet werden.

### <a name="storage-optimized"></a>Speicheroptimiert

Die [VM-Größen vom Typ „Datenspeicheroptimiert“](../../../virtual-machines/sizes-storage.md) sind für spezifische Anwendungsfälle gedacht. Bei diesen virtuellen Computern wurden der Datenträgerdurchsatz und die E/A-Leistung speziell optimiert. Diese VM-Serie ist auf Big Data-Szenarien, Data Warehousing und große Transaktionsdatenbanken ausgelegt. 

#### <a name="lsv2-series"></a>Lsv2-Reihe

Die [Lsv2-Serie](../../../virtual-machines/lsv2-series.md) bietet hohen Durchsatz, geringe Latenz und lokalen NVMe-Speicher. Die VMs der Lsv2-Reihe sind für die Verwendung des direkt an die VM angebundenen lokalen Datenträgers auf dem Knoten optimiert, statt für die Verwendung dauerhafter Datenträger. 

Diese virtuellen Computer stellen eine sehr gute Option für Big Data, Data Warehousing, Berichterstellung und ETL-Workloads dar. Der hohe Durchsatz und IOPS-Wert des lokalen NVMe-Speichers ist ein guter Anwendungsfall zur Verarbeitung von Dateien, die in Ihre Datenbank geladen werden, sowie für andere Szenarien, in denen die Quelldaten aus dem Quellsystem oder anderen Repositorys, z. B. Azure-Blobspeicher oder Azure Data Lake, neu erstellt werden können. Die virtuellen Computer der [Lsv2-Serie](../../../virtual-machines/lsv2-series.md) können ihre Datenträgerleistung ebenfalls für jeweils bis zu 30 Minuten per Burst steigern.

Diese VM-Größen bewegen sich zwischen 8 und 80 vCPUs mit 8 GiB Arbeitsspeicher pro vCPU, und für acht vCPUs sind jeweils 1,92 TB an NVMe-SSD-Speicherplatz verfügbar. Dies bedeutet, dass für die größte VM dieser Serie ([L80s_v2](../../../virtual-machines/lsv2-series.md)) 80 vCPUs und 640 BiB Arbeitsspeicher mit 10 x 1,92 TB an NVMe-Speicherplatz zur Verfügung stehen.  Für all diese virtuellen Computer gilt einheitlich ein Arbeitsspeicher/Kern-Verhältnis von „8“.

Der NVMe-Speicher ist kurzlebig. Dies bedeutet, dass Daten auf diesen Datenträgern verloren gehen, wenn Sie Ihren virtuellen Computer neu starten.

Die Serien Lsv2 und Ls unterstützen [Storage Premium](../../../virtual-machines/premium-storage-performance.md), aber keine Storage Premium-Zwischenspeicherung. Die Erstellung eines lokalen Caches zur Erhöhung des IOPS-Werts wird nicht unterstützt. 

> [!WARNING]
> Die Speicherung Ihrer Datendateien in kurzlebigem NVMe-Speicher kann zu Datenverlust führen, wenn die Zuordnung der VM aufgehoben wird. 

### <a name="constrained-vcores"></a>Eingeschränkte virtuelle Kerne

Für SQL Server-Workloads mit hohen Leistungsanforderungen werden häufig größere Mengen an Arbeitsspeicher, E/A-Leistung und Durchsatz ohne eine höhere Anzahl virtueller Kerne benötigt. 

Bei den meisten OLTP-Workloads handelt es sich um Anwendungsdatenbanken mit einer großen Zahl an kleineren Transaktionen. Bei OLTP-Workloads wird nur eine geringe Menge der Daten gelesen oder geändert, aber die Menge an Transaktionen basierend auf der Benutzeranzahl ist deutlich größer. Es ist wichtig, dass SQL Server-Arbeitsspeicher verfügbar ist, um Pläne zwischenzuspeichern, kürzlich genutzte Daten zur Verbesserung der Leistung zu speichern und sicherzustellen, dass physische Lesevorgänge schneller in den Arbeitsspeicher eingelesen werden können. 

Für diese OLTP-Umgebungen sind eine größere Menge an Arbeitsspeicher, schneller Speicher und die benötigte E/A-Bandbreite zur Erzielung einer optimalen Leistung erforderlich. 

Um diese Leistungsebene nutzen zu können, ohne dass die höheren SQL Server-Lizenzierungskosten anfallen, werden von Azure VM-Größen mit einer [eingeschränkten vCPU-Anzahl](../../../virtual-machines/constrained-vcpu.md) angeboten. 

Auf diese Weise sind die Lizenzierungskosten kontrollierbar, indem die verfügbaren virtuellen Kerne reduziert werden, während die Werte für den Arbeitsspeicher, den Speicher und die E/A-Bandbreite des übergeordneten virtuellen Computers beibehalten werden.

Die Anzahl von vCPUs kann auf die Hälfte oder ein Viertel der ursprünglichen VM-Größe beschränkt werden. Wenn die virtuellen Kerne reduziert werden, die für den virtuellen Computer verfügbar sind, wird ein höheres Arbeitsspeicher/Kern-Verhältnis erzielt.

Diese neuen VM-Größen verfügen über ein Suffix, das die Anzahl von aktiven vCPUs angibt, um die Identifizierung zu erleichtern. 

Beispielsweise müssen für [M64-32ms](../../../virtual-machines/constrained-vcpu.md) nur 32 virtuelle SQL Server-Kerne mit den Arbeitsspeicher-, E/A- und Durchsatzwerten von [M64ms](/azure/virtual-machines/m-series) lizenziert werden, und für [M64-16ms](../../../virtual-machines/constrained-vcpu.md) ist nur die Lizenzierung von 16 virtuellen Kernen erforderlich.  Für [M64-16ms](../../../virtual-machines/constrained-vcpu.md) fällt dann zwar ein Viertel der SQL Server-Lizenzierungskosten von M64ms an, aber die Computekosten des virtuellen Computers sind identisch.

> [!NOTE] 
> - Mittlere bis große Data Warehouse-Workloads können trotzdem von [VMs mit eingeschränkten virtuellen Kernen](../../../virtual-machines/constrained-vcpu.md) profitieren. Data Warehouse-Workloads zeichnen sich häufig aber durch weniger Benutzer und Prozesse aus, bei denen größere Datenmengen mit parallel ausgeführten Abfrageplänen verarbeitet werden. 
> - Die Computekosten, zu denen auch die Lizenzierung für das Betriebssystem gehört, ändern sich gegenüber dem übergeordneten virtuellen Computer nicht. 

## <a name="storage-guidance"></a>Leitfaden für Speicher

Ausführliche Informationen zum Testen der SQL Server-Leistung auf Azure Virtual Machines mit TPC-E- und TPC-C-Benchmarks finden Sie im Blog [Optimieren der OLTP-Leistung](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). 

Für alle Produktionsworkloads wird Azure-Blobcache mit SSD Premium empfohlen. 

> [!WARNING]
> Datenträger vom Typ HDD Standard und SSD Standard verfügen über unterschiedliche Latenzen und Bandbreiten und sollten nur für Entwicklungs-/Testworkloads verwendet werden. Für Produktionsworkloads sollte SSD Premium verwendet werden.

Außerdem sollten Sie Ihr Azure-Speicherkonto im selben Rechenzentrum wie Ihre SQL Server-VMs erstellen, um Übertragungsverzögerungen zu verringern. Wenn Sie ein Speicherkonto erstellen, deaktivieren Sie die Georeplikation, da keine konsistente Schreibreihenfolge über mehrere Datenträger gewährleistet werden kann. Konfigurieren Sie stattdessen eine SQL Server-Notfallwiederherstellungstechnologie zwischen zwei Azure-Rechenzentren. Weitere Informationen finden Sie unter [Hochverfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="disks-guidance"></a>Leitfaden für Datenträger

Es gibt drei Haupttypen von Datenträgern auf Azure Virtual Machines:

* **Betriebssystemdatenträger**: Wenn Sie einen virtuellen Azure-Computer erstellen, fügt die Plattform dem virtuellen Computer mindestens einen Datenträger (mit der Bezeichnung Laufwerk **C:** ) für das Betriebssystem hinzu. Bei diesem Datenträger handelt es sich um eine VHD, die als Seitenblob gespeichert wird.
* **Temporärer Datenträger**: Virtuelle Azure-Computer enthalten einen weiteren Datenträger, der als temporärer Datenträger bezeichnet wird (Laufwerk **D:** ). Dies ist ein Datenträger für temporären Speicherbereich auf dem Knoten.
* **Datenträger für Daten**: Sie können dem virtuellen Computer auch weitere Datenträger für Daten hinzufügen. Diese werden als Seitenblobs im Speicher gespeichert.

In den folgenden Abschnitten werden Empfehlungen zur Verwendung dieser unterschiedlichen Datenträger erläutert.

### <a name="operating-system-disk"></a>Betriebssystem-Datenträger

Ein Betriebssystem-Datenträger ist eine virtuelle Festplatte, die Sie als aktive Version eines Betriebssystems starten und einbinden können, und die als Laufwerk **C** bezeichnet ist.

Die für den Betriebssystem-Datenträger verwendete Caching-Standardrichtlinie ergibt **Lesen/Schreiben**. Für leistungsabhängige Anwendungen empfiehlt sich die Verwendung von Datenträgern für Daten anstelle des Betriebssystem-Datenträgers. Weitere Informationen finden Sie im Abschnitt über Datenträger für Daten weiter unten.

### <a name="temporary-disk"></a>Temporärer Datenträger

Das temporäre Speicherlaufwerk, das als Laufwerk **D:** bezeichnet wird, wird nicht in Azure Blob Storage beibehalten. Speichern Sie die Datenbank- oder Transaktionsprotokolldateien für Ihre Benutzer nicht auf Laufwerk **D:** .

Speichern Sie TempDB für unternehmenskritische SQL Server-Workloads auf dem lokalen SSD-Laufwerk `D:\` (nachdem Sie die richtige VM-Größe ausgewählt haben). Wenn Sie die VM im Azure Portal oder mit Azure-Schnellstartvorlagen erstellen und [Temp DB auf dem lokalen Datenträger gespeichert wird](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583), sind keine weiteren Maßnahmen erforderlich. In allen anderen Fällen befolgen Sie die Schritte im Blog zum [Verwenden von SSDs zum Speichern von TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-TempDB-and-buffer-pool-extensions/), um Fehler nach Neustarts zu vermeiden. Wenn die Kapazität des lokalen Laufwerks für die Größe von Temp DB nicht ausreicht, platzieren Sie Temp DB in einem Speicherpool als [Stripeset](../../../virtual-machines/premium-storage-performance.md) auf SSD Premium-Datenträgern mit [schreibgeschützter Zwischenspeicherung](../../../virtual-machines/premium-storage-performance.md#disk-caching).

Für VMs, die SSD Premium unterstützen, können Sie TempDB auch auf einem Datenträger speichern, der SSD Premium mit aktiviertem Lesecache unterstützt.


### <a name="data-disks"></a>Datenträger

* **Verwenden Sie SSD Premium-Datenträgern für Daten- und Protokolldateien**: Wenn Sie kein Datenträgerstriping verwenden, sollten Sie zwei Datenträger vom Typ SSD Premium verwenden, wobei sich auf einem Datenträger die Protokolldatei und auf dem anderen die Daten befinden. Jeder Datenträger vom Typ SSD Premium stellt je nach Größe verschiedene IOPS und Bandbreite (MB/s) bereit. Dies wird im Artikel [Auswählen eines Datenträgertyps](../../../virtual-machines/disks-types.md) beschrieben. Wenn Sie ein Verfahren zum Datenträgerstriping verwenden, z. B. Speicherplätze, erzielen Sie eine optimale Leistung, indem Sie zwei Pools einrichten, einen für die Protokolldatei(en) und den anderen für die Datendateien. Wenn Sie jedoch planen, SQL Server-Failoverclusterinstanzen (FCI) zu verwenden, müssen Sie einen Pool konfigurieren oder stattdessen [Premium-Dateifreigaben](failover-cluster-instance-premium-file-share-manually-configure.md) verwenden.

   > [!TIP]
   > - Testergebnisse zu verschiedenen Datenträger- und Workloadkonfigurationen finden Sie im folgenden Blogbeitrag: [Storage Configuration Guidelines for SQL Server on Azure VM](/archive/blogs/sqlserverstorageengine/storage-configuration-guidelines-for-sql-server-on-azure-vm) (Richtlinien zur Speicherkonfiguration für SQL Server auf Azure Virtual Machines)
   > - Erwägen Sie für unternehmenskritische Leistung für SQL Server-Instanzen, die ca. 50.000 IOPS benötigen, 10 -P30-Datenträger durch einen SSD Ultra-Datenträger zu ersetzen. Weitere Informationen finden Sie im folgenden Blogbeitrag: [Unternehmenskritische Leistung mit SSD Ultra](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > Beim Bereitstellen einer SQL Server-VM im Portal haben Sie die Möglichkeit, Ihre Speicherkonfiguration zu bearbeiten. Je nach Konfiguration konfiguriert Azure einen oder mehrere Datenträger. Mehrere Datenträger werden in einem einzelnen Speicherpool mit Striping zusammengefasst. Daten- und Protokolldateien befinden sich in dieser Konfiguration zusammen. Weitere Informationen finden Sie unter [Speicherkonfiguration für SQL Server-VMs](storage-configuration.md).

* **Datenträgerstriping**: Für einen höheren Durchsatz können Sie zusätzliche Datenträger für Daten hinzufügen und Datenträgerstriping verwenden. Um die Anzahl der Datenträger zu ermitteln, müssen Sie die Anzahl der IOPS und die Bandbreite analysieren, die für die Protokolldateien und für Ihre Daten und die TempDB-Dateien erforderlich sind. Beachten Sie, dass verschiedene VM-Größen über unterschiedliche Grenzwerte für die unterstützte Anzahl der IOPS und die Bandbreiten verfügen. Informationen finden Sie in den Tabellen zu IOPS pro [Größe des virtuellen Computers](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Verwenden Sie die folgenden Richtlinien:

  * Verwenden Sie [Speicherplätze](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)) für Windows 8 und Windows Server 2012 oder höher mit den folgenden Richtlinien:

      1. Legen Sie die Überlappung (Stripsetgröße) für OLTP-Workloads auf 64 KB (65.536 Bytes) und für Data Warehousing-Workloads auf 256 KB (262.144 Bytes) fest, um Leistungseinbußen durch falsche Partitionsausrichtung zu vermeiden. Dies muss mit PowerShell festgelegt werden.
      2. Festgelegte Anzahl der Spalten = Anzahl der physischen Datenträger. Verwenden Sie PowerShell, wenn Sie mehr als 8 Datenträger (keine Server-Manager-Benutzeroberfläche) konfigurieren. 

    Mit PowerShell erstellen Sie z. B. wie folgt einen neuen Speicherpool mit einer Überlappungsgröße von 64 KB und einer Anzahl der Spalten, die der Anzahl der physischen Datenträger im Speicherpool entspricht:

    ```powershell
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
    
    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" `
        -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
        -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
        –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
        -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
        -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Für Windows 2008 R2 oder früher können Sie dynamische Datenträger (Betriebssystem-Stripesetvolumes) verwenden, und die Stripesetgröße ist immer 64 KB. Diese Option ist seit Windows 8/Windows Server 2012 veraltet. Informationen hierzu finden Sie in der Supporterklärung unter [Virtual Disk Service is transitioning to Windows Storage Management API](/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api)(Übergang des Diensts für virtuelle Datenträger in die Windows-Speicherverwaltungs-API, in englischer Sprache).

  * Bei Verwendung von [Direkte Speicherplätze](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) (Storage Spaces Direct, S2D) mit [SQL Server-Failoverclusterinstanzen](failover-cluster-instance-storage-spaces-direct-manually-configure.md) müssen Sie einen einzelnen Pool konfigurieren. Auch wenn für einen einzelnen Pool unterschiedliche Volumes erstellt werden können, weisen diese alle die gleichen Merkmale (z.B. die gleiche Cacherichtlinie) auf.

  * Bestimmen Sie auf Basis der erwarteten Auslastung die Anzahl der jedem Speicherpool zugeordneten Datenträger. Bedenken Sie, dass verschiedene VM-Größen unterschiedlich viele angefügte Datenträger für Daten unterstützen. Weitere Informationen finden Sie unter [Größen für virtuelle Computer](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Wenn Sie keine Datenträger vom Typ SSD Premium nutzen (Entwicklungs-/Testszenarien), sollten Sie die für Ihre [Größe des virtuellen Computers](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) maximal unterstützte Anzahl von Datenträgern für Daten hinzufügen und Datenträgerstriping verwenden.

* **Cacherichtlinie**: Berücksichtigen Sie die folgenden Empfehlungen für die Cacherichtlinie (abhängig von Ihrer Speicherkonfiguration).

  * Wenn Sie separate Datenträger für Daten- und Protokolldateien verwenden, aktivieren Sie das Zwischenspeichern für Lesevorgänge für die Datenträger, auf denen Ihre Datendateien und TempDB-Datendateien gehostet werden. Dadurch lässt sich die Leistung mitunter erheblich verbessern. Aktivieren Sie die Zwischenspeicherung nicht für den Datenträger mit der Protokolldatei, da sich dadurch die Leistung minimal verschlechtert.

  * Wenn Sie Datenträgerstriping in einem einzelnen Speicherpool verwenden, profitieren die meisten Workloads vom Zwischenspeichern für Lesevorgänge. Wenn Sie gesonderte Speicherpools für die Protokoll- und Datendateien haben, aktivieren Sie die Zwischenspeicherung für Lesevorgänge nur für den Speicherpool der Datendateien. Bei bestimmten schreiblastigen Workloads lässt sich unter Umständen ohne Zwischenspeicherung eine höhere Leistung erzielen. Dies kann nur im Rahmen von Tests ermittelt werden.

  * Die obigen Empfehlungen gelten für SSD Premium-Datenträger. Wenn Sie keine SSD Premium-Datenträger verwenden, sollten Sie das Caching für reguläre Datenträger nicht aktivieren.

  * Informationen zum Konfigurieren des Datenträgercachings finden Sie in den folgenden Artikeln. Für das klassische Bereitstellungsmodell (ASM): [Set-AzureOSDisk](/previous-versions/azure/jj152847(v=azure.100)) und [Set-AzureDataDisk](/previous-versions/azure/jj152851(v=azure.100)). Weitere Informationen zum Azure Resource Manager-Bereitstellungsmodell finden Sie hier: [Set-AzOSDisk](/powershell/module/az.compute/set-azvmosdisk) und [Set-AzVMDataDisk](/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Beenden Sie den SQL Server-Dienst beim Ändern der Cacheeinstellung der Azure Virtual Machines-Datenträger, um eine Beschädigung der Datenbank zu vermeiden.

* **Größe der NTFS-Zuordnungseinheiten**: Beim Formatieren des Datenträgers für Daten empfiehlt es sich, als Größe der Zuordnungseinheiten für Daten- und Protokolldateien sowie für TempDB einen Wert von 64 KB zu verwenden. Wenn tempdb auf dem temporären Datenträger (Laufwerk „D:\“) platziert wird, macht der Leistungsgewinn, der durch die Nutzung dieses Laufwerks erzielt wird, die Notwendigkeit einer Zuordnungseinheitsgröße von 64 KB mehr als wett. 

* **Bewährte Methoden für die Datenträgerverwaltung**: Beenden Sie den SQL Server-Dienst, wenn Sie einen Datenträger für Daten entfernen oder dessen Cachetyp ändern. Wenn die Cacheeinstellungen auf dem Betriebssystemdatenträger geändert werden, beendet Azure den virtuellen Computer, ändert den Cachetyp und startet den virtuellen Computer neu. Wenn die Cacheeinstellungen eines Datenträgers für Daten geändert werden, wird der virtuelle Computer nicht beendet, aber der Datenträger für Daten wird während der Änderung vom virtuellen Computer getrennt und dann erneut angefügt.

  > [!WARNING]
  > Beenden Sie den SQL Server-Dienst während dieser Vorgänge nicht, kann eine Beschädigung der Datenbank die Folge sein.


## <a name="io-guidance"></a>E/A-Leitfaden

* Die besten Ergebnisse mit SSD Premium-Datenträgern werden erzielt, wenn Sie Ihre Anwendung und Anforderungen parallelisieren. SSD Premium ist für Szenarien bestimmt, in denen die E/A-Warteschlangentiefe größer als 1 ist, sodass nur geringe oder gar keine Leistungssteigerungen für serielle Singlethread-Anforderungen erreicht werden (auch wenn sie speicherintensiv sind). Dies kann sich z. B. auf die Singlethread-Testergebnisse der Leistungsanalysetools, z. B. SQLIO, auswirken.

* Ziehen Sie die Verwendung der [Datenbankseitenkomprimierung](/sql/relational-databases/data-compression/data-compression) in Betracht, weil Sie dadurch die Leistung von E/A-intensiven Workloads verbessern können. Die Datenkomprimierung erhöht jedoch möglicherweise den CPU-Verbrauch auf dem Datenbankserver.

* Erwägen Sie die Aktivierung der sofortigen Dateiinitialisierung, um die für die anfängliche Dateizuordnung erforderliche Zeit zu verringern. Um die sofortige Dateiinitialisierung nutzen zu können, gewähren Sie dem SQL Server-Dienstkonto (MSSQLSERVER) die Berechtigung SE_MANAGE_VOLUME_NAME, und fügen Sie es zur Sicherheitsrichtlinie **Durchführen von Volumewartungsaufgaben** hinzu. Wenn Sie ein SQL Server-Plattformimage für Azure verwenden, wird das Standarddienstkonto (NT Service\MSSQLSERVER) nicht der Sicherheitsrichtlinie **Durchführen von Volumewartungsaufgaben** hinzugefügt. Anders ausgedrückt: Die sofortige Dateiinitialisierung wird in einem SQL Server Azure-Plattformimages nicht aktiviert. Starten Sie den SQL Server-Dienst neu, nachdem Sie das SQL Server-Dienstkonto der Sicherheitsrichtlinie **Durchführen von Volumewartungsaufgaben** hinzugefügt haben. Möglicherweise sind bei Verwendung dieses Features bestimmte Sicherheitsaspekte zu berücksichtigen. Weitere Informationen finden Sie unter [Datenbankdatei-Initialisierung](/sql/relational-databases/databases/database-instant-file-initialization).

* **Automatische Vergrößerung** gilt lediglich als Behelfslösung bei unerwartetem Wachstum. Führen Sie die tägliche Verwaltung Ihres Daten- und Protokolldateiwachstums nicht durch automatische Vergrößerung. Wenn die automatische Vergrößerung verwendet wird, vergrößern Sie die Datei vorab mit dem Switch "Größe".

* Stellen Sie sicher, dass **Automatische Verkleinerung** deaktiviert ist, um unnötigen Mehraufwand zu vermeiden, der sich negativ auf die Leistung auswirken kann.

* Verschieben Sie alle Datenbanken, einschließlich der Systemdatenbanken, auf Datenträger für Daten. Weitere Informationen finden Sie unter [Verschieben von Systemdatenbanken](/sql/relational-databases/databases/move-system-databases).

* Verschieben Sie die Verzeichnisse für das SQL Server-Fehlerprotokoll und die Ablaufverfolgungsdateien auf die Datenträger für Daten. Dies können Sie mit dem SQL Server-Konfigurations-Manager vornehmen, indem Sie mit der rechten Maustaste auf die SQL Server-Instanz klicken und „Eigenschaften“ auswählen. Die Einstellungen für Fehlerprotokolle und Ablaufverfolgungsdateien können auf der Registerkarte **Startparameter** geändert werden. Das Sicherungsverzeichnis wird auf der Registerkarte **Erweitert** angegeben. Der folgende Screenshot zeigt, wo sich der Startparameter für Fehlerprotokolle befindet.

    ![Screenshot eines SQL-Fehlerprotokolls](./media/performance-guidelines-best-practices/sql_server_error_log_location.png)

* Richten Sie standardmäßige Dateispeicherorte für Sicherungen und Datenbanken ein. Verwenden Sie die Empfehlungen in diesem Artikel, und nehmen Sie die Änderungen im Fenster mit den Servereigenschaften vor. Eine Anleitung finden Sie unter [Anzeigen oder Ändern der Standardspeicherorte für Daten- und Protokolldateien (SQL Server Management Studio)](/sql/database-engine/configure-windows/view-or-change-the-default-locations-for-data-and-log-files). Der folgende Screenshot zeigt, wo diese Änderungen vorgenommen werden.

    ![Datenprotokoll- und Sicherungsdateien für SQL](./media/performance-guidelines-best-practices/sql_server_default_data_log_backup_locations.png)
* Aktivieren Sie gesperrte Seiten, um E/A- und Auslagerungsaktivitäten zu verringern. Weitere Informationen finden Sie unter [Aktivieren der Option zum Sperren von Seiten im Speicher (Windows)](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).

* Wenn Sie SQL Server 2012 ausführen, installieren Sie Service Pack 1, kumulatives Update 10. Dieses Update enthält ein Fix für schlechte E/A-Leistung beim Ausführen von SELECT in temporären Tabellenanweisungen in SQL Server 2012 Informationen hierzu finden Sie in diesem [Knowledge Base-Artikel](https://support.microsoft.com/kb/2958012).

* Es könnte auch sinnvoll sein, alle Datendateien beim Übertragen in bzw. aus Azure zu komprimieren.

## <a name="feature-specific-guidance"></a>Featurespezifische Anleitungen

Für manche Bereitstellungen können durch Verwendung erweiterter Konfigurationsmethoden zusätzliche Leistungsvorteile erzielt werden. In der folgenden Liste sind einige SQL Server-Features zusammengestellt, mit denen Sie eine bessere Leistung erzielen können:

### <a name="back-up-to-azure-storage"></a>Sicherung in Azure Storage
Wenn Sie Sicherungen für eine SQL Server-Instanz durchführen, die auf Azure Virtual Machines ausgeführt wird, können Sie die [SQL Server-Sicherung über URLs](/sql/relational-databases/backup-restore/sql-server-backup-to-url) verwenden. Dieses Feature steht ab SQL Server 2012 SP1 CU2 zur Verfügung und wird für Sicherungen auf die angefügten Datenträgern für Daten empfohlen. Bei Sicherungen/Wiederherstellungen nach/aus Azure Storage folgen Sie den Empfehlungen unter [SQL Server-URL-Sicherung – bewährte Methoden und Problembehandlung](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting) und „Wiederherstellen von in Microsoft Azure gespeicherten Sicherungen“. Darüber hinaus können Sie diese Sicherungen mit [automatisierten Sicherungen für SQL Server auf Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md) automatisieren.

In Versionen vor SQL Server 2012 können Sie das [Microsoft SQL Server Backup to Microsoft Azure Tool](https://www.microsoft.com/download/details.aspx?id=40740)verwenden. Dieses Tool hilft, den Sicherungsdurchsatz durch Verwenden mehrerer Sicherungsstripeset-Ziele zu erhöhen.

### <a name="sql-server-data-files-in-azure"></a>SQL Server-Datendateien in Azure

Das neue Feature [SQL Server-Datendateien in Azure](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) ist ab SQL Server 2014 verfügbar. Das Ausführen von SQL Server mit Datendateien in Azure zeigt vergleichbare Leistungseigenschaften wie die Verwendung von Azure-Datenträgern.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Failoverclusterinstanz und Speicherplätze

Wenn Sie Speicherplätze verwenden, deaktivieren Sie beim Hinzufügen von Knoten zum Cluster auf der Seite **Bestätigung** das Kontrollkästchen **Der gesamte geeignete Speicher soll dem Cluster hinzugefügt werden**. 

![Deaktivieren von geeignetem Speicher](./media/performance-guidelines-best-practices/uncheck-eligible-cluster-storage.png)

Wenn Sie Speicherplätze verwenden und das Kontrollkästchen **Der gesamte geeignete Speicher soll dem Cluster hinzugefügt werden.** nicht deaktivieren, trennt Windows die virtuellen Datenträger während des Clusterprozesses. Sie werden daher erst im Datenträger-Manager oder -Explorer angezeigt, nachdem die Speicherplätze aus dem Cluster entfernt und mithilfe von PowerShell erneut angefügt wurden. Mit Speicherplätzen werden mehrere Datenträger in Speicherpools gruppiert. Weitere Informationen finden Sie unter [Speicherplätze](/windows-server/storage/storage-spaces/overview).

## <a name="multiple-instances"></a>Mehrfache Instanzen 

Beachten Sie die folgenden Best Practices, wenn Sie mehrere SQL Server-Instanzen für eine einzige VM bereitstellen: 

- Legen Sie den maximalen Serverarbeitsspeicher für jede SQL Server-Instanz fest, und stellen Sie dabei sicher, dass genügend Arbeitsspeicher für das Betriebssystem verbleibt. Aktualisieren Sie die Arbeitsspeichereinschränkungen für die SQL Server-Instanzen, wenn Sie Änderungen an der zugeordneten Arbeitsspeicherkapazität für die VM vornehmen. 
- Konfigurieren Sie separate LUNs für Daten, Protokolle und TempDB, da für diese Elemente unterschiedliche Workloadmuster gelten und verhindert werden sollte, dass sie sich gegenseitig beeinträchtigen. 
- Testen Sie Ihre Umgebung unter produktionsähnlichen Bedingungen, um zu gewährleisten, dass das System maximale Workloads im Rahmen Ihrer Anwendungs-SLAs bewältigen kann. 

Anzeichen für eine Systemüberlastung könnten u. a. zu wenige Arbeitsthreads, langsame Antwortzeiten und/oder Systemarbeitsspeicherprobleme bei angehaltenem Verteiler sein. 



## <a name="collect-performance-baseline"></a>Erfassen einer Leistungsbaseline

Ein ausführlicherer Ansatz besteht darin, per Systemmonitor/Logman die Daten von Leistungsindikatoren zu sammeln und SQL Server-Wartestatistiken zu erfassen, um allgemeine Belastungen und potenzielle Engpässe der Quellumgebung besser zu verstehen. 

Erfassen Sie zunächst die Daten zu CPU, Arbeitsspeicher, [IOPS](../../../virtual-machines/premium-storage-performance.md#iops), [Durchsatz](../../../virtual-machines/premium-storage-performance.md#throughput) und [Latenz](../../../virtual-machines/premium-storage-performance.md#latency) der Quellworkload zu Spitzenzeiten, indem Sie die [Prüfliste für Anforderungen an die Anwendungsleistung](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist) verwenden. 

Erfassen Sie Daten während der Spitzenzeiten, z. B. von Workloads eines typischen Geschäftstags, aber auch andere Prozesse mit hoher Auslastung, z. B. Verarbeitungsabläufe am Ende des Tages und ETL-Workloads an Wochenenden. Erwägen Sie, Ihre Ressourcen für außergewöhnlich hohe Workloads, z. B. die Verarbeitung für den Quartalsabschluss, hochzuskalieren und nach Abarbeitung der Workload dann wieder herunterzuskalieren. 

Verwenden Sie die Leistungsanalyse, um die [VM-Größe](../../../virtual-machines/sizes-memory.md) auszuwählen, mit der die erforderliche Skalierung für die Leistungsanforderungen der Workload möglich ist.


### <a name="iops-and-throughput"></a>IOPS und Durchsatz

Die SQL Server-Leistung hängt stark vom E/A-Subsystem ab. Sofern Ihre Datenbank nicht in den physischen Speicher passt, werden Datenbankseiten von SQL Server ständig in den Pufferpool hinein und wieder heraus verschoben. Die Datendateien für SQL Server sollten anders behandelt werden. Der Zugriff auf Protokolldateien wird sequenziell durchgeführt. Wenn für eine Transaktion aber ein Rollback ausgeführt werden muss, wird auf die Datendateien, einschließlich tempdb, nach dem Zufallsprinzip zugegriffen. Falls Sie über ein langsames E/A-Subsystem verfügen, kann es für Ihre Benutzer zu Leistungsproblemen kommen, z. B. zu langen Reaktionszeiten und Fehlern bei Aufgaben aufgrund von Timeouts. 

Die virtuellen Azure Marketplace-Computer verfügen über Protokolldateien auf einem physischen Datenträger, der standardmäßig getrennt von den Datendateien vorhanden ist. Anzahl und Größe der tempdb-Datendateien sind an den bewährten Methoden ausgerichtet und zielen auf das kurzlebige Laufwerk D:/ ab. 

Die folgenden Leistungsindikatoren des Systemmonitors können als Hilfe beim Überprüfen des E/A-Durchsatzes dienen, der für Ihre SQL Server-Instanz erforderlich ist: 
* **\LogicalDisk\Disk Reads/Sec** (Lese- und Schreib-IOPS)
* **\LogicalDisk\Disk Writes/Sec** (Lese- und Schreib-IOPS) 
* **\LogicalDisk\Disk Bytes/Sec** (Durchsatzanforderungen für die Daten-, Protokoll- und tempdb-Dateien)

Evaluieren Sie anhand der IOPS- und Durchsatzanforderungen zu Spitzenzeiten die VM-Größen, deren Kapazität zu Ihren Messwerten passt. 

Wenn Ihre Workload 20 K Lese-IOPS und 10 K Schreib-IOPS zu Spitzenzeiten erfordert, können Sie entweder E16s_v3 (mit bis zu 32 K zwischengespeicherten und 25.600 nicht zwischengespeicherten IOPS) oder M16_s (mit bis zu 20 K zwischengespeicherten und 10 K nicht zwischengespeicherten IOPS) mit zwei P30-Datenträgern mit „Speicherplätze“ auswählen. 

Stellen Sie sicher, dass Sie sowohl die Durchsatz- als auch die IOPS-Anforderungen der Workload verstehen, da VMs unterschiedliche Skalierungsgrenzen für IOPS und Durchsatz aufweisen.

### <a name="memory"></a>Arbeitsspeicher

Verfolgen Sie sowohl den vom Betriebssystem verwendeten externen Arbeitsspeicher als auch den von SQL Server intern verwendeten Arbeitsspeicher. Wenn Sie die Belastung für eine der Komponenten ermitteln, können Sie die Größe der virtuellen Computer leichter auswählen und Optimierungsmöglichkeiten identifizieren. 

Die folgenden Leistungsindikatoren des Systemmonitors können Ihnen als Hilfe beim Überprüfen der Speicherintegrität eines virtuellen SQL Server-Computers dienen: 
* [\Arbeitsspeicher\Verfügbare MB](/azure/monitoring/infrastructure-health/vmhealth-windows/winserver-memory-availmbytes)
* [\SQLServer:Speicher-Manager\Zielserverspeicher (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer:Speicher-Manager\Serverspeicher gesamt (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [SQLServer:Puffer-Manager\Verzögerte Schreibvorgänge/Sekunde](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer:Puffer-Manager\Seitenlebenserwartung](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)

### <a name="compute--processing"></a>Compute/Verarbeitung

Die Computeverwaltung wird in Azure anders als in einer lokalen Umgebung durchgeführt. Lokale Server sind auf mehrere Jahre ohne Upgrade ausgelegt. Dies liegt am Mehraufwand für die Verwaltung und an den Kosten für die Beschaffung neuer Hardware. Durch die Virtualisierung werden einige dieser Probleme beseitigt, aber die Anwendungen sind so optimiert, dass die zugrunde liegende Hardware bestmöglich genutzt wird. Dies bedeutet, dass bei jeder größeren Änderung des Ressourcenverbrauchs die gesamte physische Umgebung neu abgestimmt werden muss. 

In Azure ist dies nicht schwierig, weil ein neuer virtueller Computer auf anderer Hardware – oder sogar in einer anderen Region – leicht eingerichtet werden kann. 

In Azure besteht das Ziel darin, so viele VM-Ressourcen wie möglich zu nutzen. Daher sollten virtuelle Azure-Computer so konfiguriert sein, dass die durchschnittliche CPU-Auslastung so hoch wie möglich ist, ohne dass dadurch die Workload beeinträchtigt wird. 

Die folgenden Leistungsindikatoren des Systemmonitors können Ihnen als Hilfe beim Überprüfen der Computeintegrität eines virtuellen SQL Server-Computers dienen:
* **\Processor Information(_Total)\% Processor Time**
* **\Process(sqlservr)\% Processor Time**

> [!NOTE] 
> Idealerweise sollten Sie versuchen, 80 % Ihrer Computeleistung zu nutzen, während die Spitzenauslastung oberhalb von 90 % liegen kann, ohne längere Zeit 100 % zu erreichen. Im Grunde genommen sollten Sie nur die Computeleistung bereitstellen, die von der Anwendung benötigt wird, und dann planen, je nach den geschäftlichen Anforderungen hoch- bzw. herunterzuskalieren. 

## <a name="next-steps"></a>Nächste Schritte

Bewährte Methoden für die Sicherheit finden Sie unter [Sicherheitsüberlegungen für SQL Server auf Azure Virtual Machines](security-considerations-best-practices.md).

Weitere Artikel zu virtuellen SQL Server-Computern finden Sie unter [Übersicht zu SQL Server auf virtuellen Azure-Computern](sql-server-on-azure-vm-iaas-what-is-overview.md). Falls Sie Fragen zu SQL Server-VMs haben, finden Sie in den [häufig gestellten Fragen](frequently-asked-questions-faq.md) weitere Informationen.
