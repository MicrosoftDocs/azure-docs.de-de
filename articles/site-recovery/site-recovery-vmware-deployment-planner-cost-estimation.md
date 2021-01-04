---
title: Überprüfen von Kostenvorkalkulationen im Azure Site Recovery-Bereitstellungsplaner
description: Dieser Artikel beschreibt, wie Sie die Kostenvorkalkulation im Azure Site Recovery-Bereitstellungsplaner für die VMware-Notfallwiederherstellung überprüfen.
author: rajeswari-mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 7/29/2019
ms.author: ramamill
ms.openlocfilehash: dba585fbadca479c146ad42ac1bc5aa9d5349f49
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186704"
---
# <a name="review-cost-estimations-in-the-vmware-deployment-planner"></a>Überprüfen von Kostenvorkalkulationen im VMware-Bereitstellungsplaner 

Der Bereitstellungsplaner-Bericht enthält eine Zusammenfassung der Kostenvorkalkulation auf Arbeitsblättern mit [Recommendations](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) (Empfehlungen) sowie eine ausführliche Kostenanalyse auf dem Arbeitsblatt „Cost Estimation“ (Kostenvorkalkulation). Er enthält eine ausführliche Kostenanalyse pro VM. 

>[!Note]
>Die aktuelle Version des Bereitstellungsplaners (V2.5) bietet eine Kostenschätzung für VMs, die eine Replikation auf verwaltete Datenträger durchführen.

### <a name="cost-estimation-summary"></a>Zusammenfassung „Cost Estimation“ (Kostenvorkalkulation) 
Im Graphen ist die Übersicht über die geschätzten Gesamtkosten der Notfallwiederherstellung (Disaster Recovery, DR) in Azure für Ihre gewählte Zielregion und Währung dargestellt, die Sie für die Berichterstellung angegeben haben.
Zusammenfassung „Cost Estimation“ (Kostenvorkalkulation)

![Zusammenfassung „Cost Estimation“ (Kostenvorkalkulation)](media/site-recovery-vmware-deployment-planner-analyze-report/cost-estimation-summary-v2a.png)

Die Zusammenfassung erleichtert das Verständnis der Kosten, die für Speicher, Compute, Netzwerk und Lizenz anfallen, wenn Sie Ihre gesamten kompatiblen VMs mit Azure Site Recovery in Azure schützen. Die Kosten werden für kompatible VMs berechnet, nicht für alle VMs, für die eine Profilerstellung durchgeführt wurde.  
 
Sie können die Kosten entweder monatlich oder jährlich anzeigen. Erfahren Sie mehr zu [unterstützten Zielregionen](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions) und [unterstützten Währungen](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies).

**Cost by components** (Kosten nach Komponenten): Die Gesamtkosten für die Notfallwiederherstellung sind in vier Komponenten unterteilt: Compute-, Speicher-, Netzwerk- und Azure Site Recovery-Lizenzkosten. Die Kosten werden basierend auf der Nutzung berechnet, die während der Replikations- und DR-Drillvorgänge für die Bereiche Compute, Speicher (Premium und Standard), konfigurierte ExpressRoute/VPN-Verbindung zwischen lokalem Standort und Azure sowie Azure Site Recovery-Lizenz anfallen.

**Cost by states** (Kosten nach Zustand): Die Gesamtkosten für die Notfallwiederherstellung werden basierend auf zwei unterschiedlichen Zuständen kategorisiert: Replikation und DR-Drill. 

**Replication cost** (Replikationskosten):  Die Kosten, die bei der Replikation anfallen. Hierin sind die Kosten für Speicher, Netzwerk und Azure Site Recovery-Lizenz enthalten. 

**DR-Drill cost** (Kosten für DR-Drills): Die Kosten, die bei einem Testfailover anfallen. Während des Testfailovers startet Azure Site Recovery virtuelle Computer (VMs). Die Kosten für DR-Drills decken die Compute- und Speicherkosten für die ausgeführten VMs ab. 

**Azure storage cost per Month/Year** (Azure-Speicherkosten pro Monat/Jahr): Hier werden die Speichergesamtkosten angezeigt, die bei der Replikation und bei DR-Drills für Storage Premium und Standardspeicher anfallen.

## <a name="detailed-cost-analysis"></a>Ausführliche Kostenanalyse
Die Azure-Preise für die Bereiche Compute, Speicher, Netzwerk usw. variieren je nach Azure-Region. Sie können einen Bericht zur Kostenvorkalkulation mit den aktuellen Azure-Preisen generieren, der auf Ihrem Abonnement, dem Angebot zu Ihrem Abonnement und der angegebenen Azure-Zielregion in der gewählten Währung basiert. Standardmäßig werden für das Tool die Azure-Region „USA, Westen 2“ und US-Dollar (USD) als Währung verwendet. Falls Sie eine andere Region und Währung verwendet haben, werden bei der nächsten Erstellung eines Berichts ohne Abonnement-ID, Angebots-ID, Zielregion und Währung die Preise der letzten Zielregion und Währung für die Kostenvorkalkulation herangezogen.
In diesem Abschnitt werden die Abonnement-ID und Angebots-ID angezeigt, die Sie für die Berichterstellung verwendet haben.  Wenn diese Angaben nicht genutzt werden, sind die Felder leer.

Für den gesamten Bericht gilt, dass die grau markierten Zellen schreibgeschützt sind. Zellen in Weiß können Sie gemäß Ihren Anforderungen ändern.

![Details der Kostenvorkalkulation – 1](media/site-recovery-hyper-v-deployment-planner-cost-estimation/cost-estimation1-h2a.png)

### <a name="overall-dr-cost-by-components"></a>Overall DR cost by components (Gesamtkosten der Notfallwiederherstellung nach Komponenten)
Im ersten Abschnitt werden die Kosten für die Notfallwiederherstellung nach Komponenten und Zuständen angezeigt. 

**Compute** : Kosten für IaaS-VMs, die in Azure zu Notfallwiederherstellungszwecken ausgeführt werden. Hierin sind VMs enthalten, die von Azure Site Recovery bei DR-Drills (Testfailovern) erstellt werden, und VMs, die in Azure ausgeführt werden, z.B. SQL Server mit Always On-Verfügbarkeitsgruppen und Domänencontrollern/Domänennamenservern.

**Storage** : Kosten für den Azure-Speicherverbrauch für Notfallwiederherstellungszwecke. Hierin ist die Speichernutzung für Replikationsvorgänge und DR-Drills enthalten.
Netzwerk: Die Kosten für die ExpressRoute- und Site-to-Site-VPN-Verbindung für Notfallwiederherstellungszwecke. 

**ASR license** (ASR-Lizenz): Azure Site Recovery-Lizenzkosten für alle kompatiblen VMs. Wenn Sie eine VM manuell in die Tabelle für die ausführliche Kostenanalyse eingegeben haben, sind auch die Azure Site Recovery-Lizenzkosten für diese VM enthalten.

### <a name="overall-dr-cost-by-states"></a>Overall DR cost by states (Gesamtkosten der Notfallwiederherstellung nach Zuständen)
Die Gesamtkosten der Notfallwiederherstellung werden basierend auf zwei unterschiedlichen Zuständen kategorisiert: Replikation und DR-Drill.

**Replication cost** (Replikationskosten): Die Kosten, die während der Replikation anfallen. Hierin sind die Kosten für Speicher, Netzwerk und Azure Site Recovery-Lizenz enthalten. 

**DR-Drill cost** (Kosten für DR-Drills): Die Kosten, die bei DR-Drills anfallen. Bei DR-Drills startet Azure Site Recovery virtuelle Computer. Die Kosten für DR-Drills decken die Compute- und Speicherkosten für die ausgeführten VMs ab.
DR-Drill-Gesamtdauer eines Jahres = Anzahl von DR-Drills × einzelne DR-Drill-Dauer (Tage) Durchschnittliche DR-Drillkosten (pro Monat) = Gesamtkosten der DR-Drills / 12

### <a name="storage-cost-table"></a>Storage cost table (Tabelle mit Speicherkosten):
In dieser Tabelle sind die Kosten für Storage Premium und Standardspeicher für Replikation und DR-Drills mit und ohne Preisrabatt angegeben.

### <a name="site-to-azure-network"></a>Site to Azure network (Site-zu-Azure-Netzwerk)
Wählen Sie die Einstellungen gemäß Ihren Anforderungen aus. 

**ExpressRoute** : Das Tool wählt standardmäßig den ExpressRoute-Plan aus, der der erforderlichen Netzwerkbandbreite für die Deltareplikation am ehesten entspricht. Sie können den Plan gemäß Ihren Anforderungen ändern.

**VPN Gateway** (VPN-Gateway): Wählen Sie das VPN-Gateway aus, sofern ein solches in Ihrer Umgebung vorhanden ist. Standardmäßig ist „NA“ (Nicht verfügbar) festgelegt.

**Target Region** (Zielregion): Angegebene Azure-Region für die Notfallwiederherstellung. Der im Bericht verwendete Preis für Compute, Speicher, Netzwerk und Lizenz basiert auf den Azure-Preisen für diese Region. 

### <a name="vm-running-on-azure"></a>VM running on Azure (VM unter Azure)
Wenn Sie einen Domänencontroller oder eine DNS-VM/SQL Server-VM mit Always On-Verfügbarkeitsgruppen in Azure für die Notfallwiederherstellung ausführen, können Sie die Anzahl von VMs und die Größe angeben, um die entsprechenden Computingkosten in den DR-Gesamtkosten zu berücksichtigen. 

### <a name="apply-overall-discount-if-applicable"></a>Apply overall discount if applicable (Gesamtrabatt anwenden (falls zutreffend))
Wenn Sie ein Azure-Partner oder Kunde sind und Ihnen ein allgemeiner Rabatt auf Azure-Preise zusteht, können Sie dieses Feld verwenden. Das Tool wendet den Rabatt (in %) auf alle Komponenten an.

### <a name="number-of-virtual-machines-type-and-compute-cost-per-year"></a>Number of virtual machines type and compute cost (per year) (Anzahl von VM-Typ und Computekosten (pro Jahr))
In dieser Tabelle werden die Anzahl von Windows-VMs und anderen VMs sowie die dazugehörigen Computekosten für DR-Drills angezeigt.

### <a name="settings"></a>Einstellungen 

**Currency** : Die Währung, in der der Bericht erstellt wird. „Cost duration“ (Kostendauer):  Sie können alle Kosten für den Monat oder für das ganze Jahr anzeigen. 

## <a name="detailed-cost-analysis-table"></a>Tabelle „Detailed cost analysis“ (Ausführliche Kostenanalyse)
![Detailed cost analysis](media/site-recovery-hyper-v-deployment-planner-cost-estimation/detailed-cost-analysis-h2a.png) (Ausführliche Kostenanalyse): In dieser Tabelle sind die Kosten für die einzelnen kompatiblen VMs aufgeschlüsselt. Sie können diese Tabelle auch verwenden, um die geschätzten Kosten für die Azure-Notfallwiederherstellung für VMs ohne Profilerstellung zu erhalten, wenn Sie VMs manuell hinzufügen. Dies ist hilfreich, wenn Sie die Azure-Kosten für eine neue Bereitstellung der Notfallwiederherstellung kalkulieren müssen, ohne dass eine ausführliche Profilerstellung durchgeführt wird.
Gehen Sie wie folgt vor, um VMs manuell hinzuzufügen: 
1.  Klicken Sie auf die Schaltfläche „Insert row“ (Zeile einfügen), um zwischen der Start- und Endzeile eine neue Zeile einzufügen.

2.  Fügen Sie in die folgenden Spalten Werte zur ungefähren VM-Größe und Anzahl von VMs für diese Konfiguration ein: 

* Number of VMs, IaaS size (Your selection) (Anzahl von VMs, IaaS-Größe (Ihre Auswahl))
* Storage Type (Standard/Premium) (Speichertyp (Standard/Premium))
* VM total storage size (GB) of the source machine (VM-Gesamtspeichergröße des Quellcomputers in GB)
* Number of DR drills in a year (Anzahl von DR-Drills in einem Jahr) 
* Each DR drill duration (Days) (Dauer einzelner DR-Drills (Tage)) 
* Betriebssystemtyp
* Datenredundanz 
* Azure-Hybridvorteil

1. Sie können auf alle VMs der Tabelle den gleichen Wert anwenden, indem Sie für „Number of DR drills in a year“, „Each DR drill duration (Days)“, „Data redundancy“ und „Azure Hybrid Use Benefit“ auf die Schaltfläche „Apply to all“ (Auf alle anwenden) klicken.

1. Klicken Sie auf „Re-calculate cost“ (Kosten neu berechnen), um die Kosten zu aktualisieren.

**VM Name:** Der Name der VM.

**Number of VMs** (Anzahl von VMs): Die Anzahl von VMs, die mit der Konfiguration übereinstimmen. Sie können die Anzahl von vorhandenen VMs aktualisieren, wenn für VMs mit ähnlicher Konfiguration keine Profilerstellung durchgeführt wird, diese VMs aber geschützt werden.

**IaaS size (Recommendation)** (IaaS-Größe [Empfehlung]): Dies ist die VM-Rollengröße der kompatiblen VM, die vom Tool empfohlen wird. 

**IaaS size (Your selection)** (IaaS-Größe [Ihre Auswahl]): Standardmäßig entspricht diese Größenangabe der empfohlenen VM-Rollengröße. Sie können die Rolle gemäß Ihren Anforderungen ändern. Die Computekosten basieren auf Ihrer gewählten VM-Rollengröße.

**Storage type** (Speichertyp): Der Typ des Speichers, der von der VM verwendet wird. Es kann entweder Standardspeicher oder Storage Premium ausgewählt werden.

**VM total storage size (GB)** (VM-Gesamtspeichergröße [GB]): Der Gesamtspeicher der Quell-VM.

**Number of DR drills in a year** (Anzahl von DR-Drills in einem Jahr): Gibt an, wie oft Sie in einem Jahr DR-Drills durchführen. Die Standardeinstellung ist viermal pro Jahr. Sie können den Zeitraum für bestimmte VMs ändern oder den neuen Wert auf alle VMs anwenden, indem Sie den neuen Wert in der obersten Zeile eingeben und auf die Schaltfläche „Apply to all“ (Auf alle anwenden) klicken. Die Gesamtkosten für DR-Drills werden basierend auf der Anzahl von DR-Drills in einem Jahr und den einzelnen Dauern der DR-Drills berechnet.  

**Each DR-Drill duration (Days)** (Dauer einzelner DR-Drills [Tage]): Die Dauer für jeden DR-Drill. Standardmäßig ist als Wert hier „7 Tage alle 90 Tage“ angegeben. Dies entspricht dem [Software Assurance-Vorteil für die Notfallwiederherstellung](https://azure.microsoft.com/pricing/details/site-recovery). Sie können den Zeitraum für bestimmte VMs ändern oder einen neuen Wert auf alle VMs anwenden, indem Sie den neuen Wert in der obersten Zeile eingeben und auf die Schaltfläche „Apply to all“ (Auf alle anwenden) klicken. Die Gesamtkosten für DR-Drills werden basierend auf der Anzahl von DR-Drills in einem Jahr und der Dauer der einzelnen DR-Drills berechnet.
  
**OS Type** (Betriebssystemtyp): Der Betriebssystemtyp der VM. Er kann entweder „Windows“ oder „Linux“ lauten. Wenn als Betriebssystemtyp „Windows“ angegeben ist, kann der Azure-Vorteil bei Hybridnutzung auf die VM angewendet werden. 

**Data redundancy** (Datenredundanz): Dies kann einer der folgenden Werte sein: „Locally redundant storage (LRS)“ (Lokal redundanter Speicher), „Geo-redundant storage (GRS)“ (Georedundanter Speicher) oder „Read-access geo-redundant storage (RA-GRS)“ (Georedundanter Speicher mit Lesezugriff). LRS ist die Standardeinstellung. Sie können den Typ basierend auf Ihrem Speicherkonto für bestimmte VMs ändern, oder Sie können den neuen Typ auf alle VMs anwenden, indem Sie den Typ in obersten Zeile ändern und auf die Schaltfläche „Apply to all“ (Auf alle anwenden) klicken.  Die Speicherkosten für die Replikation werden basierend auf dem Preis für die Datenredundanz berechnet, die Sie ausgewählt haben. 

**Azure Hybrid Benefit** (Azure-Hybridvorteil): Sie können den Azure-Hybridvorteil auf virtuelle Windows-Computer anwenden, falls zutreffend.  Die Standardeinstellung ist „Ja“. Sie können die Einstellung für bestimmte VMs ändern oder alle VMs aktualisieren, indem Sie auf die Schaltfläche „Auf alle anwenden“ klicken.

**Total Azure consumption** (Azure-Gesamtverbrauch): Umfasst Compute-, Speicher- und Azure Site Recovery-Lizenzkosten für Ihre Notfallwiederherstellung. Basierend auf Ihrer Auswahl werden die Kosten entweder monatlich oder jährlich angezeigt.

**Steady state replication cost** (Kosten für Replikation im stabilen Zustand): Dies schließt die Speicherkosten für die Replikation ein.

**Total DR-Drill cost (average)** (Gesamtkosten für DR-Drill [Durchschnitt]): Umfasst die Compute- und Speicherkosten für den DR-Drill.

**ASR license cost** (ASR-Lizenzkosten): Azure Site Recovery-Lizenzkosten.

## <a name="supported-target-regions"></a>Unterstützte Zielregionen
Mit dem Azure Site Recovery-Bereitstellungsplaner kann eine Kostenvorkalkulation für die unten angegebenen Azure-Regionen erstellt werden. Falls Ihre Region unten nicht aufgeführt ist, können Sie eine der folgenden Regionen verwenden, deren Preise Ihrer Region am ehesten entsprechen.

eastus, eastus2, westus, centralus, northcentralus, southcentralus, northeurope, westeurope, eastasia, southeastasia, japaneast, japanwest, australiaeast, australiasoutheast, brazilsouth, southindia, centralindia, westindia, canadacentral, canadaeast, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth 

## <a name="supported-currencies"></a>Unterstützte Währungen
Der Azure Site Recovery-Bereitstellungsplaner kann den Kostenbericht für die folgenden Währungen erstellen:

|Währung|Name|Währung|Name|Währung|Name|
|---|---|---|---|---|---|---|---|
|ARS|Argentinische Pesos ($)|AUD|Australischer Dollar ($)|BRL|Brasilianischer Real (R$)|
|CAD|Kanadischer Dollar ($)|CHF|Schweizer Franken (chf)|DKK|Dänische Krone (kr)|
|EUR|Euro (€)|GBP|Britisches Pfund (£)|HKD|Hongkong-Dollar (HK$)|
|IDR|Indonesische Rupiah (Rp)|INR|Indische Rupie (₹)|JPY|Japanischer Yen (¥)|
|KRW|Südkoreanische Won (₩)|MXN|Mexikanischer Peso (MX$)|MYR|Malaysischer Ringgit (RM$)|
|NOK|Norwegische Krone (kr)|NZD|Neuseeländischer Dollar ($)|RUB|Russische Rubel (руб)|
|SAR|Saudi-Riyal (SR)|SEK|Schwedische Krone (kr)|TWD|Taiwanesische Dollar (NT$)|
|TRY|Türkische Lira (TL)|USD| US-Dollar ($)|ZAR|Südafrikanischer Rand (R)|

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Schutz finden Sie unter [Einrichten der Notfallwiederherstellung in Azure für lokale VMware-VMs](./vmware-azure-tutorial.md).
