---
title: Unterstützte Szenarien für SAP HANA in Azure (große Instanzen) | Microsoft Docs
description: Unterstützte Szenarien und ihre Architekturdetails für SAP HANA in Azure (große Instanzen)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/06/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 501c5ffa86f2360e44c187e087f7285bbf4084fd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60477705"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Unterstützte Szenarien für große HANA-Instanzen
Dieses Dokument beschreibt die unterstützten Szenarien sowie deren Architekturdetails für große HANA-Instanzen (HANA Large Instances, HLI).

>[!NOTE]
>Wenn Ihr erforderliches Szenario hier nicht beschrieben wird, wenden Sie sich an das Microsoft Service Management-Team, um Ihre Anforderungen zu bewerten.
Bevor Sie mit der Bereitstellung der HLI-Einheit fortfahren, überprüfen Sie den Entwurf zusammen mit SAP oder Ihrem Dienstimplementierungspartner.

## <a name="terms-and-definitions"></a>Begriffe und Definitionen
Machen Sie sich mit den Begriffen und Definitionen vertraut, die in diesem Dokument verwendet werden.

- SID: Der Systembezeichner für das HANA-System.
- HLI: Große HANA-Instanzen.
- DR: Ein Standort für die Notfallwiederherstellung (Disaster Recovery).
- Normale DR: Ein Systemsetup mit einer dedizierten Ressource, die nur für DR-Zwecke verwendet wird.
- Mehrzweck-DR: Ein System am DR-Standort, das für die Verwendung einer Nicht-Produktionsumgebung zusammen mit einer Produktionsinstanz konfiguriert ist, die für die Verwendung des DR-Ereignisses konfiguriert ist. 
- Einzelne SID:  Ein System mit einer installierten Instanz.
- Mehrfach-SID: Ein System mit mehreren konfigurierten Instanzen. Wird auch als MCOS-Umgebung bezeichnet.


## <a name="overview"></a>Übersicht
Große HANA-Instanzen unterstützen eine Vielzahl von Architekturen, um Ihren Geschäftsanforderungen zu genügen. In der folgenden Liste werden die Szenarien und ihre Konfigurationsdetails beschrieben. 

Der abgeleiteten Architekturentwurf ist ausschließlich aus der Infrastrukturperspektive zu verstehen, und Sie müssen sich mit SAP oder Ihren Implementierungspartnern hinsichtlich der HANA-Bereitstellung beraten. Wenn Ihre Szenarien nicht aufgeführt werden, wenden Sie sich an das Microsoft-Kontoteam, um die Architektur zu überprüfen und eine Lösung für Sie zu ermitteln.

**Diese Architekturen sind vollständig kompatibel mit dem TDI-Entwurf (Tailored Data Integration, angepasste Datenintegration) und werden von SAP unterstützt.**

Dieses Dokument beschreibt die Details der beiden Komponenten in jeder unterstützten Architektur:

- Ethernet
- Storage

### <a name="ethernet"></a>Ethernet

Jeder bereitgestellte Server ist mit den Gruppen von Ethernet-Schnittstellen vorkonfiguriert. Im Folgenden finden Sie die Details zu den Ethernet-Schnittstellen, die für die einzelnen HLI-Einheiten konfiguriert sind.

- **A:** Diese Schnittstelle wird für/durch einen Clientzugriff verwendet.
- **B**: Diese Schnittstelle wird für die Kommunikation zwischen den Knoten verwendet. Diese Schnittstelle ist auf allen Servern (unabhängig von der angeforderten Topologie) konfiguriert, wird aber nur für 
- Szenarien mit horizontaler Skalierung verwendet.
- **C**: Diese Schnittstelle wird für die Konnektivität zwischen Knoten und Speicher verwendet.
- **D**: Diese Schnittstelle wird für die Konnektivität zwischen Knoten und iSCSI-Geräteverbindung für das STONITH-Setup verwendet. Diese Schnittstelle ist nur konfiguriert, wenn das HSR-Setup angefordert wird.  

| LOGISCHE NIC-SCHNITTSTELLEN | SKU-TYP | Name mit dem Betriebssystem SUSE | Name mit dem Betriebssystem RHEL | Anwendungsfall|
| --- | --- | --- | --- | --- |
| Eine Datei | TYP I | eth0.tenant | eno1.tenant | Client zu HLI |
| b | TYP I | eth2.tenant | eno3.tenant | Knoten zu Knoten |
| C | TYP I | eth1.tenant | eno2.tenant | Knoten zu Speicher |
| D | TYP I | eth4.tenant | eno4.tenant | STONITH |
| Eine Datei | TYP II | vlan\<tenantNo> | team0.tenant | Client zu HLI |
| b | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Knoten zu Knoten |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Knoten zu Speicher |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | STONITH |

Sie verwenden die Schnittstellen abhängig von der Topologie, die für die HLI-Einheit konfiguriert ist. Beispielsweise ist die Schnittstelle „B“ für die Kommunikation zwischen Knoten eingerichtet. Dies ist sinnvoll, wenn Sie eine horizontal skalierte Topologie konfiguriert haben. Bei einer Konfiguration mit zentraler Hochskalierung eines Knotens wird diese Schnittstelle nicht verwendet. Überprüfen Sie Ihre erforderlichen Szenarien (weiter unten in diesem Dokument), um weitere Informationen zur Schnittstellenverwendung zu erhalten. 

Bei Bedarf können Sie zusätzliche eigene Netzwerkkarten definieren. Die Konfiguration für die vorhandenen NICs kann jedoch NICHT geändert werden.

>[!NOTE]
>Sie ermitteln möglicherweise noch weitere Schnittstellen, die physische Schnittstellen oder Verbindungen sind. Sie sollten die oben genannten Schnittstellen für Ihren Verwendungsfall in Betracht ziehen. Der Rest kann ignoriert bzw. beibehalten werden.

Die Verteilung für Einheiten mit zwei zugewiesenen IP-Adressen sollte wie folgt aussehen:

- Ethernet „A“ muss eine zugewiesene IP-Adresse haben, die aus dem Server-IP-Pool-Adressbereich stammt, den Sie an Microsoft übermittelt haben. Diese IP-Adresse muss zur Verwaltung in „/etc/hosts“ des Betriebssystems verwendet werden.

- Ethernet „C“ muss eine zugewiesene IP-Adresse haben, die für die Kommunikation mit NFS verwendet wird. Daher muss diese Adresse **NICHT** in „etc/hosts“ verwaltet werden, um Instanz-zu-Instanz-Datenverkehr im Mandanten zu ermöglichen.

Für Bereitstellungsfälle von HANA-Systemreplikation oder horizontal skaliertem HANA ist eine Bladekonfiguration mit zwei IP-Adressen nicht geeignet. Wenn Sie nur zwei zugewiesene IP-Adressen haben, aber eine solche Konfiguration bereitstellen möchten, wenden Sie sich an das SAP HANA in Azure-Dienstverwaltungsteam, um eine dritte IP-Adresse zu erhalten, die in einem dritten VLAN zugewiesen ist. Für Einheiten von HANA (große Instanzen) mit drei zugewiesenen IP-Adressen auf drei Netzwerkkartenports gelten die folgenden Verwendungsregeln:

- Ethernet „A“ muss eine zugewiesene IP-Adresse haben, die aus dem Server-IP-Pool-Adressbereich stammt, den Sie an Microsoft übermittelt haben. Daher darf diese IP-Adresse nicht zur Verwaltung in „/etc/hosts“ des Betriebssystems verwendet werden.

- Ethernet „B“ muss ausschließlich verwendet werden, um in „etc/hosts“ für die Kommunikation zwischen den verschiedenen Instanzen verwaltet zu werden. Diese Adressen wären auch die IP-Adressen, die in horizontal skalierten HANA-Konfigurationen als IP-Adressen verwaltet werden müssen, die HANA für die Konfiguration zwischen Knoten verwendet.

- Ethernet „C“ muss eine zugewiesene IP-Adresse haben, die für die Kommunikation mit dem NFS-Speicher verwendet wird. Daher darf dieser Typ von Adressen nicht in „etc/hosts“ verwaltet werden.

- Ethernet „D“ sollte ausschließlich für den Zugriff auf das STONITH-Gerät für den Schrittmacher verwendet werden. Diese Schnittstelle ist erforderlich, wenn Sie HANA-Systemreplikation (HSR) konfigurieren und automatisches Failover im Betriebssystem mit einem SBD-basierten Gerät erzielen möchten.


### <a name="storage"></a>Storage
Der Speicher ist je nach angeforderter Topologie vorkonfiguriert. Die Volumegrößen und der Bereitstellungspunkt variieren basierend auf der Anzahl der Server, den SKUs und der konfigurierten Topologie. Überprüfen Sie Ihre erforderlichen Szenarien (weiter unten in diesem Dokument), um weitere Informationen zu erhalten. Wenn mehr Speicher erforderlich ist, können Sie diesen in Inkrementen von einem TB erwerben.

>[!NOTE]
>Der Bereitstellungspunkt „/usr/sap/\<SID>“ ist eine symbolische Verknüpfung mit dem Bereitstellungspunkt „/hana/shared“.


## <a name="supported-scenarios"></a>Unterstützte Szenarien

In den Architekturdiagrammen werden die folgenden Notationen für die Grafiken verwendet:

![Legends.PNG](media/hana-supported-scenario/Legends.PNG)

Die folgende Liste nennt die unterstützten Szenarien:

1. Einzelner Knoten mit einer SID
2. Einzelner Knoten MCOS
3. Einzelner Knoten mit DR (normal)
4. Einzelner Knoten mit DR (Mehrzweck)
5. HSR mit STONITH
6. HSR mit DR (normal/Mehrzweck) 
7. Automatisches Hostfailover (1+1) 
8. Horizontales Hochskalieren mit Standby
9. Horizontales Hochskalieren ohne Standby
10. Horizontales Hochskalieren mit DR



## <a name="1-single-node-with-one-sid"></a>1. Einzelner Knoten mit einer SID

Diese Topologie unterstützt einen Knoten in einer zentral hochskalierten Konfiguration mit einer SID.

### <a name="architecture-diagram"></a>Architekturdiagramm  

![Single-node-with-one-SID.png](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
Die folgenden Netzwerkschnittstellen sind vorkonfiguriert:

| LOGISCHE NIC-SCHNITTSTELLEN | SKU-TYP | Name mit dem Betriebssystem SUSE | Name mit dem Betriebssystem RHEL | Anwendungsfall|
| --- | --- | --- | --- | --- |
| Eine Datei | TYP I | eth0.tenant | eno1.tenant | Client zu HLI |
| b | TYP I | eth2.tenant | eno3.tenant | Konfiguriert, aber nicht verwendet |
| C | TYP I | eth1.tenant | eno2.tenant | Knoten zu Speicher |
| D | TYP I | eth4.tenant | eno4.tenant | Konfiguriert, aber nicht verwendet |
| Eine Datei | TYP II | vlan\<tenantNo> | team0.tenant | Client zu HLI |
| b | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Konfiguriert, aber nicht verwendet |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Knoten zu Speicher |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfiguriert, aber nicht verwendet |

### <a name="storage"></a>Storage
Die folgenden Bereitstellungspunkte sind vorkonfiguriert:

| Bereitstellungspunkt | Anwendungsfall | 
| --- | --- |
|/hana/shared/SID | HANA-Installation | 
|/hana/data/SID/mnt00001 | Datendateieninstallation | 
|/hana/log/SID/mnt00001 | Protokolldateieninstallation | 
|/hana/logbackups/SID | Wiederholungsprotokolle |

### <a name="key-considerations"></a>Wichtige Aspekte
- /usr/sap/SID ist eine symbolische Verknüpfung mit /hana/shared/SID.

## <a name="2-single-node-mcos"></a>2. Einzelner Knoten MCOS

Diese Topologie unterstützt einen Knoten in einer zentral hochskalierten Konfiguration mit mehreren SIDs.

### <a name="architecture-diagram"></a>Architekturdiagramm  

![single-node-mcos.png](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
Die folgenden Netzwerkschnittstellen sind vorkonfiguriert:

| LOGISCHE NIC-SCHNITTSTELLEN | SKU-TYP | Name mit dem Betriebssystem SUSE | Name mit dem Betriebssystem RHEL | Anwendungsfall|
| --- | --- | --- | --- | --- |
| Eine Datei | TYP I | eth0.tenant | eno1.tenant | Client zu HLI |
| b | TYP I | eth2.tenant | eno3.tenant | Konfiguriert, aber nicht verwendet |
| C | TYP I | eth1.tenant | eno2.tenant | Knoten zu Speicher |
| D | TYP I | eth4.tenant | eno4.tenant | Konfiguriert, aber nicht verwendet |
| Eine Datei | TYP II | vlan\<tenantNo> | team0.tenant | Client zu HLI |
| b | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Konfiguriert, aber nicht verwendet |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Knoten zu Speicher |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfiguriert, aber nicht verwendet |

### <a name="storage"></a>Storage
Die folgenden Bereitstellungspunkte sind vorkonfiguriert:

| Bereitstellungspunkt | Anwendungsfall | 
| --- | --- |
|/hana/shared/SID1 | HANA-Installation für SID1 | 
|/hana/data/SID1/mnt00001 | Datendateieninstallation für SID1 | 
|/hana/log/SID1/mnt00001 | Protokolldateieninstallation für SID1 | 
|/hana/logbackups/SID1 | Wiederholungsprotokolle für SID1 |
|/hana/shared/SID2 | HANA-Installation für SID2 | 
|/hana/data/SID2/mnt00001 | Datendateieninstallation für SID2 | 
|/hana/log/SID2/mnt00001 | Protokolldateieninstallation für SID2 | 
|/hana/logbackups/SID2 | Wiederholungsprotokolle für SID2 |

### <a name="key-considerations"></a>Wichtige Aspekte
- /usr/sap/SID ist eine symbolische Verknüpfung mit /hana/shared/SID.
- Die Verteilung der Volumegröße basiert auf der Datenbankgröße im Arbeitsspeicher. Im Abschnitt [Übersicht und Architektur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) erfahren Sie, welche Datenbankgrößen im Arbeitsspeicher in einer Umgebung mit mehreren SIDs unterstützt werden.

## <a name="3-single-node-with-dr-normal"></a>3. Einzelner Knoten mit DR (normal)
 
Diese Topologie unterstützt einen Knoten in einer zentral hochskalierten Konfiguration mit einer SID oder mehreren SIDs mit der speicherbasierten Replikation in den DR-Standort für eine primäre SID. In der Abbildung wird nur eine SID am primären Standort dargestellt, aber mehrere SIDs (MCOS) werden ebenfalls unterstützt.

### <a name="architecture-diagram"></a>Architekturdiagramm  

![Single-node-with-dr.png](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
Die folgenden Netzwerkschnittstellen sind vorkonfiguriert:

| LOGISCHE NIC-SCHNITTSTELLEN | SKU-TYP | Name mit dem Betriebssystem SUSE | Name mit dem Betriebssystem RHEL | Anwendungsfall|
| --- | --- | --- | --- | --- |
| Eine Datei | TYP I | eth0.tenant | eno1.tenant | Client zu HLI |
| b | TYP I | eth2.tenant | eno3.tenant | Konfiguriert, aber nicht verwendet |
| C | TYP I | eth1.tenant | eno2.tenant | Knoten zu Speicher |
| D | TYP I | eth4.tenant | eno4.tenant | Konfiguriert, aber nicht verwendet |
| Eine Datei | TYP II | vlan\<tenantNo> | team0.tenant | Client zu HLI |
| b | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Konfiguriert, aber nicht verwendet |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Knoten zu Speicher |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfiguriert, aber nicht verwendet |

### <a name="storage"></a>Storage
Die folgenden Bereitstellungspunkte sind vorkonfiguriert:

| Bereitstellungspunkt | Anwendungsfall | 
| --- | --- |
|/hana/shared/SID | HANA-Installation für SID | 
|/hana/data/SID/mnt00001 | Datendateieninstallation für SID | 
|/hana/log/SID/mnt00001 | Protokolldateieninstallation für SID | 
|/hana/logbackups/SID | Wiederholungsprotokolle für SID |


### <a name="key-considerations"></a>Wichtige Aspekte
- /usr/sap/SID ist eine symbolische Verknüpfung mit /hana/shared/SID.
- Für MCOS: Die Verteilung der Volumegröße basiert auf der Datenbankgröße im Arbeitsspeicher. Im Abschnitt [Übersicht und Architektur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) erfahren Sie, welche Datenbankgrößen im Arbeitsspeicher in einer Umgebung mit mehreren SIDs unterstützt werden.
- Am DR-Standort: Die Volumes und Bereitstellungspunkte sind für die Installation der HANA-Produktionsinstanz an der DR-HLI Einheit konfiguriert (als „Für HANA-Installation erforderlich“ markiert). 
- Am DR-Standort: Die Daten, Protokollsicherungen und die freigegebenen Volumes (als „Speicherreplikation“ markiert) werden über eine Momentaufnahme aus dem Produktionsstandort repliziert. Diese Volumes werden nur während der Failoverzeit bereitgestellt. Weitere Informationen finden Sie in dem Dokument [Failoverprozedur für die Notfallwiederherstellung](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery).
- Das Startvolume für die **SKU-Typ I-Klasse** wird auf den DR-Knoten repliziert.


## <a name="4-single-node-with-dr-multipurpose"></a>4. Einzelner Knoten mit DR (Mehrzweck)
 
Diese Topologie unterstützt einen Knoten in einer zentral hochskalierten Konfiguration mit einer SID oder mehreren SIDs mit der speicherbasierten Replikation in den DR-Standort für eine primäre SID. In der Abbildung wird nur eine SID am primären Standort dargestellt, aber mehrere SIDs (MCOS) werden ebenfalls unterstützt. Am DR-Standort wird die HLI-Einheit für die QA-Instanz verwendet, während die Produktionsvorgänge vom primären Standort aus ausgeführt werden. Zum Zeitpunkt des DR-Failovers (oder Failovertests) wird die QA-Instanz am DR-Standort außer Betrieb genommen.

### <a name="architecture-diagram"></a>Architekturdiagramm  

![single-node-with-dr-multipurpose.png](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
Die folgenden Netzwerkschnittstellen sind vorkonfiguriert:

| LOGISCHE NIC-SCHNITTSTELLEN | SKU-TYP | Name mit dem Betriebssystem SUSE | Name mit dem Betriebssystem RHEL | Anwendungsfall|
| --- | --- | --- | --- | --- |
| Eine Datei | TYP I | eth0.tenant | eno1.tenant | Client zu HLI |
| b | TYP I | eth2.tenant | eno3.tenant | Konfiguriert, aber nicht verwendet |
| C | TYP I | eth1.tenant | eno2.tenant | Knoten zu Speicher |
| D | TYP I | eth4.tenant | eno4.tenant | Konfiguriert, aber nicht verwendet |
| Eine Datei | TYP II | vlan\<tenantNo> | team0.tenant | Client zu HLI |
| b | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Konfiguriert, aber nicht verwendet |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Knoten zu Speicher |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfiguriert, aber nicht verwendet |

### <a name="storage"></a>Storage
Die folgenden Bereitstellungspunkte sind vorkonfiguriert:

| Bereitstellungspunkt | Anwendungsfall | 
| --- | --- |
|**Am primären Standort**|
|/hana/shared/SID | SID der HANA-Installation für die Produktion | 
|/hana/data/SID/mnt00001 | SID der Datendateieninstallation für die Produktion | 
|/hana/log/SID/mnt00001 | SID der Protokolldateieninstallation für die Produktion | 
|/hana/logbackups/SID | SID für die Wiederholungsprotokolle für die Produktion |
|**Am DR-Standort**|
|/hana/shared/SID | SID der HANA-Installation für die Produktion | 
|/hana/data/SID/mnt00001 | SID der Datendateieninstallation für die Produktion | 
|/hana/log/SID/mnt00001 | SID der Protokolldateieninstallation für die Produktion | 
|/hana/shared/QA-SID | HANA-Installation für QA-SID | 
|/hana/data/QA-SID/mnt00001 | Datendateieninstallation für QA-SID | 
|/hana/log/QA-SID/mnt00001 | Protokolldateieninstallation für QA-SID |
|/hana/logbackups/QA-SID | Wiederholungsprotokolle für QA-SID |

### <a name="key-considerations"></a>Wichtige Aspekte
- /usr/sap/SID ist eine symbolische Verknüpfung mit /hana/shared/SID.
- Für MCOS: Die Verteilung der Volumegröße basiert auf der Datenbankgröße im Arbeitsspeicher. Im Abschnitt [Übersicht und Architektur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) erfahren Sie, welche Datenbankgrößen im Arbeitsspeicher in einer Umgebung mit mehreren SIDs unterstützt werden.
- Am DR-Standort: Die Volumes und Bereitstellungspunkte sind für die Installation der HANA-Produktionsinstanz an der DR-HLI Einheit konfiguriert (als „Für HANA-Installation erforderlich“ markiert). 
- Am DR-Standort: Die Daten, Protokollsicherungen und die freigegebenen Volumes (als „Speicherreplikation“ markiert) werden über eine Momentaufnahme aus dem Produktionsstandort repliziert. Diese Volumes werden nur während der Failoverzeit bereitgestellt. Weitere Informationen finden Sie in dem Dokument [Failoverprozedur für die Notfallwiederherstellung](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery). 
- Am DR-Standort: Die Daten, Protokollsicherungen, freigegebenen Volumes für QA (als „QA-Instanzinstallation“ gekennzeichnet) sind für die QA-Instanzinstallation konfiguriert.
- Das Startvolume für die **SKU-Typ I-Klasse** wird auf den DR-Knoten repliziert.

## <a name="5-hsr-with-stonith"></a>5. HSR mit STONITH
 
Diese Topologie unterstützt zwei Knoten für die Konfiguration der HANA-Systemreplikation (HSR). Diese Konfiguration wird nur für einzelne HANA-Instanzen auf einem Knoten unterstützt. Das bedeutet, dass MCOS-Szenarien NICHT unterstützt werden.

**Zurzeit wird diese Architektur nur für das Betriebssystem SUSE unterstützt.**


### <a name="architecture-diagram"></a>Architekturdiagramm  

![HSR-with-STONITH.png](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
Die folgenden Netzwerkschnittstellen sind vorkonfiguriert:

| LOGISCHE NIC-SCHNITTSTELLEN | SKU-TYP | Name mit dem Betriebssystem SUSE | Name mit dem Betriebssystem RHEL | Anwendungsfall|
| --- | --- | --- | --- | --- |
| Eine Datei | TYP I | eth0.tenant | eno1.tenant | Client zu HLI |
| b | TYP I | eth2.tenant | eno3.tenant | Konfiguriert, aber nicht verwendet |
| C | TYP I | eth1.tenant | eno2.tenant | Knoten zu Speicher |
| D | TYP I | eth4.tenant | eno4.tenant | Verwendet für STONITH |
| Eine Datei | TYP II | vlan\<tenantNo> | team0.tenant | Client zu HLI |
| b | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Konfiguriert, aber nicht verwendet |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Knoten zu Speicher |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Verwendet für STONITH |

### <a name="storage"></a>Storage
Die folgenden Bereitstellungspunkte sind vorkonfiguriert:

| Bereitstellungspunkt | Anwendungsfall | 
| --- | --- |
|**Auf dem primären Knoten**|
|/hana/shared/SID | SID der HANA-Installation für die Produktion | 
|/hana/data/SID/mnt00001 | SID der Datendateieninstallation für die Produktion | 
|/hana/log/SID/mnt00001 | SID der Protokolldateieninstallation für die Produktion | 
|/hana/logbackups/SID | SID für die Wiederholungsprotokolle für die Produktion |
|**Auf dem sekundären Knoten**|
|/hana/shared/SID | SID der HANA-Installation für sekundären Knoten | 
|/hana/data/SID/mnt00001 | SID der Datendateieninstallation für sekundären Knoten | 
|/hana/log/SID/mnt00001 | SID der Protokolldateieninstallation für sekundären Knoten | 
|/hana/logbackups/SID | SID der Wiederholungsprotokolle für sekundären Knoten |

### <a name="key-considerations"></a>Wichtige Aspekte
- /usr/sap/SID ist eine symbolische Verknüpfung mit /hana/shared/SID.
- Für MCOS: Die Verteilung der Volumegröße basiert auf der Datenbankgröße im Arbeitsspeicher. Im Abschnitt [Übersicht und Architektur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) erfahren Sie, welche Datenbankgrößen im Arbeitsspeicher in einer Umgebung mit mehreren SIDs unterstützt werden.
- STONITH: Eine SBD wird für das STONITH-Setup konfiguriert. Die Verwendung von STONITH ist jedoch optional.


## <a name="6-hsr-with-dr"></a>6. HSR mit DR
 
Diese Topologie unterstützt zwei Knoten für die Konfiguration der HANA-Systemreplikation (HSR). Sowohl normale als auch Mehrzweck-DR wird unterstützt. Diese Konfigurationen werden nur für einzelne HANA-Instanzen auf einem Knoten unterstützt. Das bedeutet, das MCOS-Szenarien mit diesen Konfigurationen NICHT unterstützt werden.

Im der Abbildung wird ein Mehrzweckszenario dargestellt. Dabei wird die HLI-Einheit am DR-Standort für die QA-Instanz verwendet, während die Produktionsvorgänge vom primären Standort aus ausgeführt werden. Zum Zeitpunkt des DR-Failovers (oder Failovertests) wird die QA-Instanz am DR-Standort außer Betrieb genommen. 



### <a name="architecture-diagram"></a>Architekturdiagramm  

![HSR-with-DR.png](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
Die folgenden Netzwerkschnittstellen sind vorkonfiguriert:

| LOGISCHE NIC-SCHNITTSTELLEN | SKU-TYP | Name mit dem Betriebssystem SUSE | Name mit dem Betriebssystem RHEL | Anwendungsfall|
| --- | --- | --- | --- | --- |
| Eine Datei | TYP I | eth0.tenant | eno1.tenant | Client zu HLI |
| b | TYP I | eth2.tenant | eno3.tenant | Konfiguriert, aber nicht verwendet |
| C | TYP I | eth1.tenant | eno2.tenant | Knoten zu Speicher |
| D | TYP I | eth4.tenant | eno4.tenant | Verwendet für STONITH |
| Eine Datei | TYP II | vlan\<tenantNo> | team0.tenant | Client zu HLI |
| b | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Konfiguriert, aber nicht verwendet |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Knoten zu Speicher |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Verwendet für STONITH |

### <a name="storage"></a>Storage
Die folgenden Bereitstellungspunkte sind vorkonfiguriert:

| Bereitstellungspunkt | Anwendungsfall | 
| --- | --- |
|**Auf dem primären Knoten am primären Standort**|
|/hana/shared/SID | SID der HANA-Installation für die Produktion | 
|/hana/data/SID/mnt00001 | SID der Datendateieninstallation für die Produktion | 
|/hana/log/SID/mnt00001 | SID der Protokolldateieninstallation für die Produktion | 
|/hana/logbackups/SID | SID für die Wiederholungsprotokolle für die Produktion |
|**Auf dem sekundären Knoten am primären Standort**|
|/hana/shared/SID | SID der HANA-Installation für sekundären Knoten | 
|/hana/data/SID/mnt00001 | SID der Datendateieninstallation für sekundären Knoten | 
|/hana/log/SID/mnt00001 | SID der Protokolldateieninstallation für sekundären Knoten | 
|/hana/logbackups/SID | SID der Wiederholungsprotokolle für sekundären Knoten |
|**Am DR-Standort**|
|/hana/shared/SID | SID der HANA-Installation für die Produktion | 
|/hana/data/SID/mnt00001 | SID der Datendateieninstallation für die Produktion | 
|/hana/log/SID/mnt00001 | SID der Protokolldateieninstallation für die Produktion | 
|/hana/shared/QA-SID | HANA-Installation für QA-SID | 
|/hana/data/QA-SID/mnt00001 | Datendateieninstallation für QA-SID | 
|/hana/log/QA-SID/mnt00001 | Protokolldateieninstallation für QA-SID |
|/hana/logbackups/QA-SID | Wiederholungsprotokolle für QA-SID |

### <a name="key-considerations"></a>Wichtige Aspekte
- /usr/sap/SID ist eine symbolische Verknüpfung mit /hana/shared/SID.
- Für MCOS: Die Verteilung der Volumegröße basiert auf der Datenbankgröße im Arbeitsspeicher. Im Abschnitt [Übersicht und Architektur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) erfahren Sie, welche Datenbankgrößen im Arbeitsspeicher in einer Umgebung mit mehreren SIDs unterstützt werden.
- STONITH: Eine SBD wird für das STONITH-Setup konfiguriert. Die Verwendung von STONITH ist jedoch optional.
- Am DR-Standort: **Zwei Sätze von Speichervolumes** sind für die Replikation des primären und sekundären Knotens erforderlich.
- Am DR-Standort: Die Volumes und Bereitstellungspunkte sind für die Installation der HANA-Produktionsinstanz an der DR-HLI Einheit konfiguriert (als „Für HANA-Installation erforderlich“ markiert). 
- Am DR-Standort: Die Daten, Protokollsicherungen und die freigegebenen Volumes (als „Speicherreplikation“ markiert) werden über eine Momentaufnahme aus dem Produktionsstandort repliziert. Diese Volumes werden nur während der Failoverzeit bereitgestellt. Weitere Informationen finden Sie in dem Dokument [Failoverprozedur für die Notfallwiederherstellung](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery). 
- Am DR-Standort: Die Daten, Protokollsicherungen, freigegebenen Volumes für QA (als „QA-Instanzinstallation“ gekennzeichnet) sind für die QA-Instanzinstallation konfiguriert.
- Das Startvolume für die **SKU-Typ I-Klasse** wird auf den DR-Knoten repliziert.


## <a name="7-host-auto-failover-11"></a>7. Automatisches Hostfailover (1+1)
 
Diese Topologie unterstützt zwei Knoten in einer automatischen Hostfailoverkonfiguration. Ein Knoten übt die Master-/Workerrolle aus, ein anderer fungiert als Standby. **SAP unterstützt dieses Szenario nur für S/4 HANA.** Weitere Details finden Sie im OSS-Hinweis „[2408419 - SAP S/4HANA: Unterstützung für mehrere Knoten](https://launchpad.support.sap.com/#/notes/2408419)“.



### <a name="architecture-diagram"></a>Architekturdiagramm  

![sca](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
Die folgenden Netzwerkschnittstellen sind vorkonfiguriert:

| LOGISCHE NIC-SCHNITTSTELLEN | SKU-TYP | Name mit dem Betriebssystem SUSE | Name mit dem Betriebssystem RHEL | Anwendungsfall|
| --- | --- | --- | --- | --- |
| Eine Datei | TYP I | eth0.tenant | eno1.tenant | Client zu HLI |
| b | TYP I | eth2.tenant | eno3.tenant | Kommunikation zwischen den Knoten |
| C | TYP I | eth1.tenant | eno2.tenant | Knoten zu Speicher |
| D | TYP I | eth4.tenant | eno4.tenant | Konfiguriert, aber nicht verwendet |
| Eine Datei | TYP II | vlan\<tenantNo> | team0.tenant | Client zu HLI |
| b | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Kommunikation zwischen den Knoten |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Knoten zu Speicher |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfiguriert, aber nicht verwendet |

### <a name="storage"></a>Storage
Die folgenden Bereitstellungspunkte sind vorkonfiguriert:

| Bereitstellungspunkt | Anwendungsfall | 
| --- | --- |
|**Auf den Master- und Standbyknoten**|
|/hana/shared | SID der HANA-Installation für die Produktion | 
|/hana/data/SID/mnt00001 | SID der Datendateieninstallation für die Produktion | 
|/hana/log/SID/mnt00001 | SID der Protokolldateieninstallation für die Produktion | 
|/hana/logbackups/SID | SID für die Wiederholungsprotokolle für die Produktion |



### <a name="key-considerations"></a>Wichtige Aspekte
- /usr/sap/SID ist eine symbolische Verknüpfung mit /hana/shared/SID.
- Auf dem Standbyknoten: Die Volumes und Bereitstellungspunkte sind für die Installation der HANA-Instanz auf der Standbyeinheit konfiguriert (als „Für HANA-Installation erforderlich“ markiert).
 

## <a name="8-scale-out-with-standby"></a>8. Horizontales Hochskalieren mit Standby
 
Diese Topologie unterstützt mehrere Knoten in einer horizontal hochskalierten Konfiguration. Ein Knoten übt die Masterrolle aus, mindestens ein Knoten die Workerrolle, und mindestens ein Knoten dient als Standby. Allerdings kann es zu einem bestimmten Zeitpunkt immer nur einen Masterknoten geben.


### <a name="architecture-diagram"></a>Architekturdiagramm  

![scaleout-nm-standby.png](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
Die folgenden Netzwerkschnittstellen sind vorkonfiguriert:

| LOGISCHE NIC-SCHNITTSTELLEN | SKU-TYP | Name mit dem Betriebssystem SUSE | Name mit dem Betriebssystem RHEL | Anwendungsfall|
| --- | --- | --- | --- | --- |
| Eine Datei | TYP I | eth0.tenant | eno1.tenant | Client zu HLI |
| b | TYP I | eth2.tenant | eno3.tenant | Kommunikation zwischen den Knoten |
| C | TYP I | eth1.tenant | eno2.tenant | Knoten zu Speicher |
| D | TYP I | eth4.tenant | eno4.tenant | Konfiguriert, aber nicht verwendet |
| Eine Datei | TYP II | vlan\<tenantNo> | team0.tenant | Client zu HLI |
| b | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Kommunikation zwischen den Knoten |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Knoten zu Speicher |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfiguriert, aber nicht verwendet |

### <a name="storage"></a>Storage
Die folgenden Bereitstellungspunkte sind vorkonfiguriert:

| Bereitstellungspunkt | Anwendungsfall | 
| --- | --- |
|**Auf den Master-, Worker- und Standbyknoten**|
|/hana/shared | SID der HANA-Installation für die Produktion | 
|/hana/data/SID/mnt00001 | SID der Datendateieninstallation für die Produktion | 
|/hana/log/SID/mnt00001 | SID der Protokolldateieninstallation für die Produktion | 
|/hana/logbackups/SID | SID für die Wiederholungsprotokolle für die Produktion |


## <a name="9-scale-out-without-standby"></a>9. Horizontales Hochskalieren ohne Standby
 
Diese Topologie unterstützt mehrere Knoten in einer horizontal hochskalierten Konfiguration. Ein Knoten übt die Masterrolle aus und mindestens ein Knoten die Workerrolle. Allerdings kann es zu einem bestimmten Zeitpunkt immer nur einen Masterknoten geben.


### <a name="architecture-diagram"></a>Architekturdiagramm  

![scaleout-nm.png](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
Die folgenden Netzwerkschnittstellen sind vorkonfiguriert:

| LOGISCHE NIC-SCHNITTSTELLEN | SKU-TYP | Name mit dem Betriebssystem SUSE | Name mit dem Betriebssystem RHEL | Anwendungsfall|
| --- | --- | --- | --- | --- |
| Eine Datei | TYP I | eth0.tenant | eno1.tenant | Client zu HLI |
| b | TYP I | eth2.tenant | eno3.tenant | Kommunikation zwischen den Knoten |
| C | TYP I | eth1.tenant | eno2.tenant | Knoten zu Speicher |
| D | TYP I | eth4.tenant | eno4.tenant | Konfiguriert, aber nicht verwendet |
| Eine Datei | TYP II | vlan\<tenantNo> | team0.tenant | Client zu HLI |
| b | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Kommunikation zwischen den Knoten |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Knoten zu Speicher |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfiguriert, aber nicht verwendet |

### <a name="storage"></a>Storage
Die folgenden Bereitstellungspunkte sind vorkonfiguriert:

| Bereitstellungspunkt | Anwendungsfall | 
| --- | --- |
|**Auf den Master- und Workerknoten**|
|/hana/shared | SID der HANA-Installation für die Produktion | 
|/hana/data/SID/mnt00001 | SID der Datendateieninstallation für die Produktion | 
|/hana/log/SID/mnt00001 | SID der Protokolldateieninstallation für die Produktion | 
|/hana/logbackups/SID | SID für die Wiederholungsprotokolle für die Produktion |


### <a name="key-considerations"></a>Wichtige Aspekte
- /usr/sap/SID ist eine symbolische Verknüpfung mit /hana/shared/SID.

## <a name="10-scale-out-with-dr"></a>10. Horizontales Hochskalieren mit DR
 
Diese Topologie unterstützt mehrere Knoten in einer horizontal hochskalierten Konfiguration mit einer DR. Sowohl normale als auch Mehrzweck-DR wird unterstützt. In der Abbildung wird nur eine DR mit einem einzigen Zweck dargestellt. Sie können diese Topologie mit oder ohne Standbyknoten anfordern.


### <a name="architecture-diagram"></a>Architekturdiagramm  

![scaleout-with-dr.png](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
Die folgenden Netzwerkschnittstellen sind vorkonfiguriert:

| LOGISCHE NIC-SCHNITTSTELLEN | SKU-TYP | Name mit dem Betriebssystem SUSE | Name mit dem Betriebssystem RHEL | Anwendungsfall|
| --- | --- | --- | --- | --- |
| Eine Datei | TYP I | eth0.tenant | eno1.tenant | Client zu HLI |
| b | TYP I | eth2.tenant | eno3.tenant | Kommunikation zwischen den Knoten |
| C | TYP I | eth1.tenant | eno2.tenant | Knoten zu Speicher |
| D | TYP I | eth4.tenant | eno4.tenant | Konfiguriert, aber nicht verwendet |
| Eine Datei | TYP II | vlan\<tenantNo> | team0.tenant | Client zu HLI |
| b | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Kommunikation zwischen den Knoten |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Knoten zu Speicher |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfiguriert, aber nicht verwendet |

### <a name="storage"></a>Storage
Die folgenden Bereitstellungspunkte sind vorkonfiguriert:

| Bereitstellungspunkt | Anwendungsfall | 
| --- | --- |
|**Auf dem primären Knoten**|
|/hana/shared | SID der HANA-Installation für die Produktion | 
|/hana/data/SID/mnt00001 | SID der Datendateieninstallation für die Produktion | 
|/hana/log/SID/mnt00001 | SID der Protokolldateieninstallation für die Produktion | 
|/hana/logbackups/SID | SID für die Wiederholungsprotokolle für die Produktion |
|**Auf den DR-Knoten**|
|/hana/shared | SID der HANA-Installation für die Produktion | 
|/hana/data/SID/mnt00001 | SID der Datendateieninstallation für die Produktion | 
|/hana/log/SID/mnt00001 | SID der Protokolldateieninstallation für die Produktion | 


### <a name="key-considerations"></a>Wichtige Aspekte
- /usr/sap/SID ist eine symbolische Verknüpfung mit /hana/shared/SID.
-  Am DR-Standort: Die Volumes und Bereitstellungspunkte sind für die Installation der HANA-Produktionsinstanz an der DR-HLI Einheit konfiguriert (als „Für HANA-Installation erforderlich“ markiert). 
- Am DR-Standort: Die Daten, Protokollsicherungen und die freigegebenen Volumes (als „Speicherreplikation“ markiert) werden über eine Momentaufnahme aus dem Produktionsstandort repliziert. Diese Volumes werden nur während der Failoverzeit bereitgestellt. Weitere Informationen finden Sie in dem Dokument [Failoverprozedur für die Notfallwiederherstellung](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery). 
- Das Startvolume für die **SKU-Typ I-Klasse** wird auf den DR-Knoten repliziert.


## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu HLI finden Sie unter [Infrastruktur und Konnektivität](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity).
- Weitere Informationen zu HLI finden Sie unter [Hochverfügbarkeit und Notfallwiederherstellung](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery).
