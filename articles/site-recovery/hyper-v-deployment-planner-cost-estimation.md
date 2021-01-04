---
title: Überprüfen der Kostenvorkalkulation des Azure Site Recovery-Bereitstellungsplaners für die Notfallwiederherstellung von Hyper-V-VMs in Azure | Microsoft-Dokumentation
description: Dieser Artikel beschreibt, wie Sie den Kostenvorkalkulationsbericht überprüfen, der vom Azure Site Recovery-Bereitstellungsplaner für die Hyper-V-Notfallwiederherstellung in Azure generiert wird.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 29457f2f5021fed9d8785f5764c4119de4be1fa9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999211"
---
# <a name="cost-estimation-report-by-azure-site-recovery-deployment-planner"></a>Azure Site Recovery-Bereitstellungsplaner-Bericht zur Kostenvorkalkulation 

Der Azure Site Recovery-Bereitstellungsplaner-Bericht enthält eine Zusammenfassung der Kostenvorkalkulation auf Arbeitsblättern mit [Recommendations](hyper-v-deployment-planner-analyze-report.md#recommendations) (Empfehlungen) sowie eine ausführliche Kostenanalyse auf dem Arbeitsblatt „Cost Estimation“ (Kostenvorkalkulation). Er enthält eine ausführliche Kostenanalyse pro VM. 

### <a name="cost-estimation-summary"></a>Zusammenfassung „Cost Estimation“ (Kostenvorkalkulation) 
Im Graphen ist die Übersicht über die geschätzten Gesamtkosten der Notfallwiederherstellung (Disaster Recovery, DR) in Azure für Ihre gewählte Zielregion und Währung dargestellt, die Sie für die Berichterstellung angegeben haben.

![Zusammenfassung „Cost Estimation“ (Kostenvorkalkulation)](media/hyper-v-azure-deployment-planner-cost-estimation/cost-estimation-summary-h2a.png)

Die Zusammenfassung erleichtert das Verständnis der Kosten, die für Speicher, Compute, Netzwerk und Lizenz anfallen, wenn Sie Ihre kompatiblen VMs mit Azure Site Recovery schützen. Die Kosten werden für kompatible VMs berechnet, nicht für alle VMs, für die eine Profilerstellung durchgeführt wurde. 
 
Sie können die Kosten entweder monatlich oder jährlich anzeigen. Erfahren Sie mehr zu [unterstützten Zielregionen](./hyper-v-deployment-planner-cost-estimation.md#supported-target-regions) und [unterstützten Währungen](./hyper-v-deployment-planner-cost-estimation.md#supported-currencies).

**Cost by components** (Kosten nach Komponenten): Die Gesamtkosten für die Notfallwiederherstellung sind in vier Komponenten unterteilt: Compute-, Speicher-, Netzwerk- und Site Recovery-Lizenzkosten. Die Kosten werden basierend auf dem Verbrauch berechnet, zu dem es während der Replikation und des DR-Drills kommt. Für die Berechnungen werden die Bereiche Compute, Speicher (Premium und Standard), die zwischen dem lokalen Standort und Azure konfigurierte ExpressRoute/VPN-Verbindung und die Site Recovery-Lizenz herangezogen.

**Cost by states** (Kosten nach Zustand): Die Kategorie für die Gesamtkosten der Notfallwiederherstellung basieren auf zwei unterschiedlichen Zuständen: Replikation und DR-Drill. 

**Replication cost** (Replikationskosten): Die Kosten, die bei der Replikation anfallen. Hierin sind die Kosten für Speicher, Netzwerk und die Site Recovery-Lizenz enthalten. 

**DR-Drill cost** (Kosten für DR-Drills): Die Kosten, die bei einem Testfailover anfallen. Während des Testfailovers startet Site Recovery virtuelle Computer (VMs). Die Kosten für DR-Drills decken die Compute- und Speicherkosten für die ausgeführten VMs ab. 

**Azure storage cost per Month/Year** (Azure-Speicherkosten pro Monat/Jahr): Die Speichergesamtkosten, die bei der Replikation und bei DR-Drills für Storage Premium und Standardspeicher anfallen.

## <a name="detailed-cost-analysis"></a>Ausführliche Kostenanalyse
Die Azure-Preise für die Bereiche Compute, Speicher und Netzwerk variieren je nach Azure-Region. Sie können einen Bericht zur Kostenvorkalkulation mit den aktuellen Azure-Preisen generieren, der auf Ihrem Abonnement, dem Angebot zu Ihrem Abonnement und der angegebenen Azure-Zielregion in der gewählten Währung basiert. Standardmäßig werden für das Tool die Azure-Region „USA, Westen 2“ und US-Dollar (USD) als Währung verwendet. Falls Sie eine andere Region und Währung verwendet haben, werden im Tool bei der nächsten Erstellung eines Berichts ohne Abonnement-ID, Angebots-ID, Zielregion und Währung die Preise der letzten Zielregion und Währung für die Kostenvorkalkulation herangezogen.

In diesem Abschnitt werden die Abonnement-ID und Angebots-ID angezeigt, die Sie für die Berichterstellung verwendet haben. Falls sie nicht verwendet werden, wird das Feld leer gelassen.

Für den gesamten Bericht gilt, dass die grau markierten Zellen schreibgeschützt sind. Zellen in Weiß können Sie gemäß Ihren Anforderungen ändern.

![Details der Kostenvorkalkulation](media/hyper-v-azure-deployment-planner-cost-estimation/cost-estimation1-h2a.png)

### <a name="overall-dr-costs-by-components"></a>Overall DR costs by components (Gesamtkosten der Notfallwiederherstellung nach Komponenten)
Im ersten Abschnitt werden die Kosten für die Notfallwiederherstellung nach Komponenten und Zuständen angezeigt. 

**Compute**: Die Kosten für IaaS-VMs, die in Azure zu Notfallwiederherstellungszwecken ausgeführt werden. Hierin sind VMs enthalten, die mit Site Recovery bei DR-Drills (Testfailovern) erstellt werden. Außerdem sind die in Azure ausgeführten VMs enthalten, z.B. SQL Server mit Always On-Verfügbarkeitsgruppen und Domänencontrollern oder Domänennamenservern.

**Storage**: Die Kosten für den Azure-Speicherverbrauch für Notfallwiederherstellungszwecke. Hierin ist die Speichernutzung für Replikationsvorgänge und DR-Drills enthalten.

**Netzwerk:** Die Kosten für die ExpressRoute- und Site-to-Site-VPN-Verbindung für Notfallwiederherstellungszwecke. 

**Azure Site Recovery-Lizenz**: Die Site Recovery-Lizenzkosten für alle kompatiblen VMs. Wenn Sie eine VM manuell in die Tabelle für die ausführliche Kostenanalyse eingegeben haben, sind auch die Site Recovery-Lizenzkosten für diese VM enthalten.

### <a name="overall-dr-costs-by-states"></a>Overall DR costs by states (Gesamtkosten der Notfallwiederherstellung nach Zuständen)
Die Gesamtkosten der Notfallwiederherstellung werden basierend auf zwei unterschiedlichen Zuständen kategorisiert: Replikation und DR-Drill.

**Replikation**: Die Kosten, die während der Replikation anfallen. Hierin sind die Kosten für Speicher, Netzwerk und die Site Recovery-Lizenz enthalten. 

**DR-Drill**: Die Kosten, die bei DR-Drills anfallen. Bei DR-Drills startet Site Recovery virtuelle Computer. Die Kosten für DR-Drills decken die Compute- und Speicherkosten für die ausgeführten VMs ab.

* DR-Drill-Gesamtdauer eines Jahres = Anzahl von DR-Drills x einzelne DR-Drill-Dauer (Tage)
* Durchschnittliche DR-Drill-Kosten (pro Monat) = DR-Drill-Kosten/12

### <a name="storage-cost-table"></a>Storage cost table (Tabelle mit Speicherkosten)
In dieser Tabelle sind die Kosten für Storage Premium und Standardspeicher für die Replikation und DR-Drills mit und ohne Preisrabatt angegeben.

### <a name="site-to-azure-network"></a>Site to Azure network (Site-zu-Azure-Netzwerk)
Wählen Sie je nach Ihren Anforderungen die passende Einstellung aus. 

**ExpressRoute**: Das Tool wählt standardmäßig den ExpressRoute-Plan aus, der der erforderlichen Netzwerkbandbreite für die Deltareplikation am ehesten entspricht. Sie können den Plan gemäß Ihren Anforderungen ändern.

**VPN Gateway type** (Typ des VPN-Gateways): Wählen Sie das Azure VPN-Gateway aus, sofern ein solches in Ihrer Umgebung vorhanden ist. Standardmäßig ist „NA“ (Nicht verfügbar) festgelegt.

**Zielregion**: Angegebene Azure-Region für die Notfallwiederherstellung. Der im Bericht verwendete Preis für Compute, Speicher, Netzwerk und Lizenz basiert auf den Azure-Preisen für diese Region. 

### <a name="vm-running-on-azure"></a>VM running on Azure (VM unter Azure)
Es kann beispielsweise sein, dass Sie über einen Domänencontroller oder eine DNS- oder SQL Server-VM mit Always On-Verfügbarkeitsgruppen verfügen, die in Azure für die Notfallwiederherstellung ausgeführt werden. Sie können die Anzahl von VMs und die Größe angeben, um die entsprechenden Computingkosten in den DR-Gesamtkosten zu berücksichtigen. 

### <a name="apply-overall-discount-if-applicable"></a>Apply overall discount if applicable (Gesamtrabatt anwenden (falls zutreffend))
Wenn Sie ein Azure-Partner oder Kunde sind und Ihnen ein allgemeiner Rabatt auf Azure-Preise zusteht, können Sie dieses Feld verwenden. Das Tool wendet den Rabatt (in Prozent) auf alle Komponenten an.

### <a name="number-of-virtual-machines-type-and-compute-cost-per-year"></a>Number of virtual machines type and compute cost (per year) (Anzahl von VM-Typ und Computekosten (pro Jahr))
In dieser Tabelle werden die Anzahl von Windows-VMs und anderen VMs sowie die dazugehörigen Computekosten für DR-Drills angezeigt.

### <a name="settings"></a>Einstellungen 
**Using managed disk** (Verwalteten Datenträger verwenden): Diese Einstellung gibt an, ob bei DR-Drills ein verwalteter Datenträger verwendet wird. Die Option ist standardmäßig auf **Ja** festgelegt. Wenn Sie **-UseManagedDisks** auf **No** festlegen, wird der Preis für den nicht verwalteten Datenträger für die Kostenkalkulation verwendet.

**Currency**: Die Währung, in der der Bericht erstellt wird.

**Cost duration** (Kostendauer): Sie können alle Kosten für den Monat oder für das ganze Jahr anzeigen. 

## <a name="detailed-cost-analysis-table"></a>Tabelle „Detailed cost analysis“ (Ausführliche Kostenanalyse)
![Ausführliche Kostenanalyse](media/hyper-v-azure-deployment-planner-cost-estimation/detailed-cost-analysis-h2a.png)

Die Tabelle enthält die Kostenaufschlüsselung für jede kompatible VM. Sie können diese Tabelle auch verwenden, um die geschätzten Kosten für die Azure-Notfallwiederherstellung für VMs ohne Profilerstellung zu erhalten, wenn Sie VMs manuell hinzufügen. Diese Informationen sind hilfreich, wenn Sie die Azure-Kosten für eine neue Bereitstellung der Notfallwiederherstellung ohne eine ausführliche Profilerstellung kalkulieren müssen.

Gehen Sie wie folgt vor, um VMs manuell hinzuzufügen:

1. Wählen Sie die Option **Insert row** (Zeile einfügen), um zwischen der Zeile **Start** und der Zeile **End** eine neue Zeile einzufügen.

1. Fügen Sie in die folgenden Spalten Werte zur ungefähren VM-Größe und die Anzahl von VMs für diese Konfiguration ein: 

    a. **Number of VMs** (Anzahl von VMs)

    b. **IaaS size (Your selection)** (IaaS-Größe (Ihre Auswahl))

    c. **Storage type Standard/Premium** (Speichertyp Standard/Premium)

    d. **VM total storage size (GB)** (VM-Gesamtspeichergröße (GB))

    e. **Number of DR drills in a year** (Anzahl von DR-Drills in einem Jahr)

    f. **Each DR-Drill duration (Days)** (Dauer einzelner DR-Drills (Tage))

    g. **OS Type** (Betriebssystemtyp)

    h. **Data redundancy** (Datenredundanz)

    i. **Azure-Hybridnutzungsvorteil**

1. Sie können auf alle VMs der Tabelle den gleichen Wert anwenden, indem Sie für **Number of DR drills in a year**, **Each DR drill duration (Days)** , **Data redundancy** und **Azure Hybrid Use Benefit** die Option **Apply to all** (Auf alle anwenden) wählen.

1. Wählen Sie **Re-calculate cost** (Kosten erneut berechnen), um die Kosten zu aktualisieren.

**VM Name:** Der Name der VM.

**Number of VMs** (Anzahl von VMs): Die Anzahl von VMs, die mit der Konfiguration übereinstimmen. Sie können die Anzahl von vorhandenen VMs aktualisieren, wenn für eine ähnliche Konfiguration mit VMs keine Profilerstellung durchgeführt wird, sondern wenn diese geschützt werden.

**IaaS size (Recommendation)** (IaaS-Größe [Empfehlung]): Die VM-Rollengröße der kompatiblen VM, die vom Tool empfohlen wird. 

**IaaS size (Your selection)** (IaaS-Größe [Ihre Auswahl]): Standardmäßig entspricht diese Größe der empfohlenen VM-Rollengröße. Sie können die Rolle gemäß Ihren Anforderungen ändern. Die Computekosten basieren auf Ihrer gewählten VM-Rollengröße.

**Storage type** (Speichertyp): Der Typ des Speichers, der von der VM verwendet wird. Es kann entweder Standardspeicher oder Storage Premium ausgewählt werden.

**VM total storage size (GB)** (VM-Gesamtspeichergröße [GB]): Der Gesamtspeicher der VM.

**Number of DR drills in a year** (Anzahl von DR-Drills in einem Jahr): Gibt an, wie oft Sie in einem Jahr DR-Drills durchführen. Standardmäßig beträgt die Anzahl viermal pro Jahr. Sie können den Zeitraum für bestimmte VMs ändern oder den neuen Wert auf alle VMs anwenden. Geben Sie den neuen Wert in der obersten Zeile ein, und wählen Sie **Apply to all** (Auf alle anwenden). Die Gesamtkosten für DR-Drills werden basierend auf der Anzahl von DR-Drills in einem Jahr und den einzelnen Dauern der DR-Drills berechnet. 

**Each DR-Drill duration (Days)** (Dauer einzelner DR-Drills [Tage]): Die Dauer für jeden DR-Drill. Standardmäßig ist als Wert hier „7 Tage alle 90 Tage“ angegeben. Dies entspricht dem [Software Assurance-Vorteil für die Notfallwiederherstellung](https://azure.microsoft.com/pricing/details/site-recovery). Sie können den Zeitraum für bestimmte VMs ändern oder einen neuen Wert auf alle VMs anwenden. Geben Sie einen neuen Wert in der obersten Zeile ein, und wählen Sie **Apply to all** (Auf alle anwenden). Die Gesamtkosten für DR-Drills werden basierend auf der Anzahl von DR-Drills in einem Jahr und der Dauer der einzelnen DR-Drills berechnet.
 
**OS Type** (Betriebssystemtyp): Der Typ des VM-Betriebssystems. Er kann entweder „Windows“ oder „Linux“ lauten. Wenn als Betriebssystemtyp „Windows“ angegeben ist, kann der Azure-Hybridnutzungsvorteil auf die VM angewendet werden. 

**Data redundancy** (Datenredundanz): Lokal redundanter Speicher, georedundanter Speicher oder schreibgeschützter georedundanter Speicher. Standardmäßig wird lokal redundanter Speicher verwendet. Sie können den Typ basierend auf Ihrem Speicherkonto für bestimmte VMs ändern, oder Sie können den neuen Typ auf alle VMs anwenden. Ändern Sie den Typ der obersten Zeile, und wählen Sie die Option **Auf alle anwenden**. Die Speicherkosten für die Replikation werden basierend auf dem Preis für die Datenredundanz berechnet, die Sie ausgewählt haben. 

**Azure Hybrid Use Benefit** (Azure-Hybridnutzungsvorteil): Sie können den Azure-Hybridvorteil auf virtuelle Windows-Computer anwenden, falls zutreffend. Die Option ist standardmäßig auf **Ja** festgelegt. Sie können die Einstellung für bestimmte VMs ändern oder alle VMs aktualisieren. Wählen Sie **Apply to all** (Auf alle anwenden).

**Total Azure consumption** (Azure-Gesamtverbrauch): Die Compute-, Speicher- und Site Recovery-Lizenzkosten für Ihre Notfallwiederherstellung. Basierend auf Ihrer Auswahl werden die Kosten entweder monatlich oder jährlich angezeigt.

**Steady state replication cost** (Kosten für Replikation im stabilen Zustand): Die Speicherkosten für die Replikation.

**Total DR-Drill cost (average)** (Gesamtkosten für DR-Drill [Durchschnitt]): Die Compute- und Speicherkosten für DR-Drills.

**Azure Site Recovery-Lizenzkosten**: Die Site Recovery-Lizenzkosten.

## <a name="supported-target-regions"></a>Unterstützte Zielregionen
Mit dem Site Recovery-Bereitstellungsplaner kann eine Kostenvorkalkulation für die unten angegebenen Azure-Regionen erstellt werden. Falls Ihre Region hier nicht aufgeführt ist, können Sie eine der folgenden Regionen verwenden, deren Preise Ihrer Region am ehesten entsprechen:

eastus, eastus2, westus, centralus, northcentralus, southcentralus, northeurope, westeurope, eastasia, southeastasia, japaneast, japanwest, australiaeast, australiasoutheast, brazilsouth, southindia, centralindia, westindia, canadacentral, canadaeast, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth 

## <a name="supported-currencies"></a>Unterstützte Währungen
Der Site Recovery-Bereitstellungsplaner kann den Kostenbericht für die folgenden Währungen erstellen:

|Währung|Name|Währung|Name|Währung|Name|
|---|---|---|---|---|---|---|---|
|ARS|Argentinische Pesos ($)|AUD|Australischer Dollar ($)|BRL|Brasilianischer Real (R$)|
|CAD|Kanadischer Dollar ($)|CHF|Schweizer Franken (CHF)|DKK|Dänische Krone (kr)|
|EUR|Euro (€)|GBP|Britisches Pfund (£)|HKD|Hongkong-Dollar (HK$)|
|IDR|Indonesische Rupiah (Rp)|INR|Indische Rupie (₹)|JPY|Japanischer Yen (¥)|
|KRW|Südkoreanische Won (₩)|MXN|Mexikanischer Peso (MX$)|MYR|Malaysischer Ringgit (RM$)|
|NOK|Norwegische Krone (kr)||NZD|Neuseeländischer Dollar ($)||RUB|Russische Rubel (руб)|
|SAR|Saudi-Riyal (SR)|SEK|Schwedische Krone (kr)|TWD|Taiwanesische Dollar (NT$)|
|TRY|Türkische Lira (TL)|USD| US-Dollar ($)|ZAR|Südafrikanischer Rand (R)|

## <a name="next-steps"></a>Nächste Schritte
Informieren Sie sich unter [Einrichten der Notfallwiederherstellung von lokalen Hyper-V-VMs in Azure](hyper-v-azure-tutorial.md) darüber, wie Sie Hyper-V-VMs in Azure mit Site Recovery schützen.
