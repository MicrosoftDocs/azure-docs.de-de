---
title: Unterstützte Szenarien für SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Unterstützte Szenarien und ihre Architekturdetails für SAP HANA in Azure (große Instanzen)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/26/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7f33ef81282dd8e60eba64cc77cbd95ab8dbc557
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967566"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Unterstützte Szenarien für große HANA-Instanzen
Dieser Artikel beschreibt die unterstützten Szenarien und Architekturdetails für große HANA-Instanzen (HANA Large Instances, HLI).

>[!NOTE]
>Wenn das Szenario, das Sie benötigen, hier nicht beschrieben wird, wenden Sie sich an das Microsoft Service Management-Team, um Ihre Anforderungen zu bewerten.
Bevor Sie die HLI-Einheit einrichten, überprüfen Sie den Entwurf zusammen mit SAP oder Ihrem Dienstimplementierungspartner.

## <a name="terms-and-definitions"></a>Begriffe und Definitionen
Machen Sie sich mit den Begriffen und Definitionen vertraut, die in diesem Artikel verwendet werden:

- **SID**: Ein Systembezeichner für das HANA-System
- **HLI**: Große HANA-Instanzen
- **DR**: Notfallwiederherstellung
- **Normale DR**: Ein Systemsetup mit einer dedizierten Ressource nur für DR-Zwecke
- **Mehrzweck-DR**: Ein System am DR-Standort, das für die Verwendung einer nicht produktiven Umgebung neben einer Produktionsinstanz konfiguriert ist, die wiederum für ein DR-Ereignis konfiguriert ist 
- **Einzelne SID**: Ein System mit einer installierten Instanz
- **Multi-SID**: Ein System mit mehreren konfigurierten Instanzen, wird auch als MCOS-Umgebung bezeichnet
- **HSR**: SAP HANA-Systemreplikation

## <a name="overview"></a>Übersicht
Große HANA-Instanzen unterstützen eine Vielzahl von Architekturen, um Ihnen bei der Erfüllung Ihrer Geschäftsanforderungen zu helfen. In den folgenden Abschnitten werden die Architekturszenarien und die zugehörigen Konfigurationsdetails beschrieben. 

Der abgeleiteten Architekturentwurf ist ausschließlich aus der Infrastrukturperspektive zu verstehen. Hinsichtlich der HANA-Bereitstellung müssen Sie sich mit SAP oder Ihren Implementierungspartnern beraten. Wenn Ihre Szenarien in diesem Artikel nicht aufgeführt sind, wenden Sie sich an das Microsoft-Kontoteam, um die Architektur zu überprüfen und eine Lösung für Sie abzuleiten.

> [!NOTE]
> Diese Architekturen sind vollständig mit dem TDI-Entwurf (Tailored Data Integration, angepasste Datenintegration) kompatibel und werden von SAP unterstützt.

Dieser Artikel beschreibt die Details der beiden Komponenten in jeder unterstützten Architektur:

- Ethernet
- Storage

### <a name="ethernet"></a>Ethernet

Jeder bereitgestellte Server ist mit Gruppen von Ethernetschnittstellen vorkonfiguriert. Die auf jeder HLI-Einheit konfigurierten Ethernetschnittstellen lassen sich in vier Kategorien unterteilen:

- **A:** Wird für oder durch Clientzugriff verwendet.
- **B**: Wird für die Kommunikation zwischen Knoten verwendet. Diese Schnittstelle ist auf allen Servern (unabhängig von der angeforderten Topologie) konfiguriert, wird aber nur für Szenarien mit horizontaler Skalierung verwendet.
- **C**: Wird für die Konnektivität zwischen Knoten und Speicher verwendet.
- **D**: Wird für die Konnektivität zwischen Knoten und iSCSI-Geräteverbindung für das STONITH-Setup verwendet. Diese Schnittstelle ist nur konfiguriert, wenn ein HSR-Setup angefordert wird.  

| Logische Netzwerkkarten-Schnittstelle | SKU-Typ | Name mit dem Betriebssystem SUSE | Name mit dem Betriebssystem RHEL | Anwendungsfall|
| --- | --- | --- | --- | --- |
| Ein | TYP I | eth0.tenant | eno1.tenant | Client zu HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Knoten zu Knoten|
| C | TYP I | eth1.tenant | eno2.tenant | Knoten zu Speicher |
| D | TYP I | eth4.tenant | eno4.tenant | STONITH |
| Ein | TYP II | vlan\<tenantNo> | team0.tenant | Client zu HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Knoten zu Knoten|
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Knoten zu Speicher |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | STONITH |

Sie wählen die Schnittstellen je nach der Topologie aus, die auf der HLI-Einheit konfiguriert ist. Beispielsweise ist die Schnittstelle „B“ für die Kommunikation zwischen Knoten eingerichtet. Dies ist sinnvoll, wenn Sie eine Topologie mit horizontaler Skalierung konfiguriert haben. Diese Schnittstelle wird für Einzelknotenkonfigurationen mit zentraler Skalierung nicht verwendet. Weitere Informationen zur Schnittstellenverwendung erhalten Sie weiter unten in diesem Artikel im jeweiligen von Ihnen benötigten Szenario. 

Bei Bedarf können Sie selbst zusätzliche Netzwerkkarten definieren. Die Konfigurationen vorhandener Netzwerkkarten *können jedoch nicht* geändert werden.

>[!NOTE]
>Möglicherweise finden Sie noch weitere Schnittstellen, die physische Schnittstellen oder Verbindungen sind. Für Ihren Anwendungsfall sollten Sie nur die oben erwähnten Schnittstellen berücksichtigen. Alle anderen können ignoriert werden.

Die Verteilung für Einheiten mit zwei zugewiesenen IP-Adressen sollte wie folgt aussehen:

- Ethernet „A“ muss eine zugewiesene IP-Adresse haben, die aus dem Adressbereich des Server-IP-Pools stammt, den Sie an Microsoft übermittelt haben. Diese IP-Adresse muss im Verzeichnis *etc/hosts* des Betriebssystems verwaltet werden.

- Ethernet „C“ muss eine zugewiesene IP-Adresse haben, die für die Kommunikation mit NFS verwendet wird. Diese Adresse muss *nicht* im Verzeichnis *etc/hosts* verwaltet werden, um Datenverkehr zwischen Instanzen innerhalb des Mandanten zu ermöglichen.

Für die HANA-Systemreplikation oder HANA-Bereitstellungen mit horizontaler Skalierung ist eine Bladekonfiguration mit zwei zugewiesenen IP-Adressen nicht geeignet. Wenn Sie nur über zwei zugewiesene IP-Adressen verfügen und eine solche Konfiguration bereitstellen möchten, wenden Sie sich an das zuständige Team von SAP HANA oder Azure Service Management. Das entsprechende Team kann Ihnen eine dritte IP-Adresse in einem dritten VLAN zuweisen. Für Einheiten großer HANA-Instanzen mit drei zugewiesenen IP-Adressen auf drei Netzwerkkartenports gelten die folgenden Verwendungsregeln:

- Ethernet „A“ muss eine zugewiesene IP-Adresse haben, die außerhalb des Adressbereichs des Server-IP-Pools liegt, den Sie an Microsoft übermittelt haben. Diese IP-Adresse darf nicht im Verzeichnis *etc/hosts* des Betriebssystems verwaltet werden.

- Ethernet „B“ muss für die Kommunikation zwischen den verschiedenen Instanzen ausschließlich im Verzeichnis *etc/hosts* verwaltet werden. Diese IP-Adressen müssen in HANA-Konfigurationen mit horizontaler Skalierung als diejenigen IP-Adressen verwaltet werden, die HANA für die Konfiguration zwischen Knoten verwendet.

- Ethernet „C“ muss eine zugewiesene IP-Adresse haben, die für die Kommunikation mit NFS-Speicher verwendet wird. Dieser IP-Adresstyp darf nicht im Verzeichnis *etc/hosts* verwaltet werden.

- Ethernet „D“ darf ausschließlich für den Zugriff auf STONITH-Geräte für Pacemaker verwendet werden. Diese Schnittstelle ist erforderlich, wenn Sie die HANA-Systemreplikation konfigurieren und ein automatisches Failover des Betriebssystems mithilfe eines SBD-basierten Geräts erzielen möchten.


### <a name="storage"></a>Storage
Der Speicher ist je nach angeforderter Topologie vorkonfiguriert. Die Volumegrößen und Bereitstellungspunkte variieren je nach Anzahl von Servern und SKUs und der konfigurierten Topologie. Weitere Informationen erhalten Sie weiter unten in diesem Artikel im jeweiligen von Ihnen benötigten Szenario. Wenn mehr Speicher erforderlich ist, können Sie diesen in Schritten von jeweils einem TB erwerben.

>[!NOTE]
>Der Bereitstellungspunkt „/usr/sap/\<SID>“ ist ein symbolischer Link zum Bereitstellungspunkt „/hana/shared“.


## <a name="supported-scenarios"></a>Unterstützte Szenarios

Bei den Architekturdiagrammen in den nächsten Abschnitten werden die folgenden Notationen verwendet:

[ ![Tabelle für Architekturdiagramme](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

Dies sind die unterstützten Szenarien:

* Einzelner Knoten mit einer SID
* Einzelner Knoten MCOS
* Einzelner Knoten mit DR (normal)
* Einzelner Knoten mit DR (Mehrzweck)
* HSR mit STONITH
* HSR mit DR (normal/Mehrzweck) 
* Automatisches Hostfailover (1+1) 
* Horizontales Hochskalieren mit Standby
* Horizontales Hochskalieren ohne Standby
* Horizontales Hochskalieren mit DR

## <a name="single-node-with-one-sid"></a>Einzelner Knoten mit einer SID

Diese Topologie unterstützt einen Knoten in einer Konfiguration mit zentraler Skalierung und einer SID.

### <a name="architecture-diagram"></a>Architekturdiagramm  

![Einzelner Knoten mit einer SID](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
Die folgenden Netzwerkschnittstellen sind vorkonfiguriert:

| Logische Netzwerkkarten-Schnittstelle | SKU-Typ | Name mit dem Betriebssystem SUSE | Name mit dem Betriebssystem RHEL | Anwendungsfall|
| --- | --- | --- | --- | --- |
| Ein | TYP I | eth0.tenant | eno1.tenant | Client zu HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Konfiguriert, aber nicht verwendet |
| C | TYP I | eth1.tenant | eno2.tenant | Knoten zu Speicher |
| D | TYP I | eth4.tenant | eno4.tenant | Konfiguriert, aber nicht verwendet |
| Ein | TYP II | vlan\<tenantNo> | team0.tenant | Client zu HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Konfiguriert, aber nicht verwendet |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Knoten zu Speicher |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfiguriert, aber nicht verwendet |

### <a name="storage"></a>Storage
Die folgenden Bereitstellungspunkte sind vorkonfiguriert:

| Mountpunkt | Anwendungsfall | 
| --- | --- |
|/hana/shared/SID | HANA-Installation | 
|/hana/data/SID/mnt00001 | Installation der Datendateien | 
|/hana/log/SID/mnt00001 | Installation der Protokolldateien | 
|/hana/logbackups/SID | Wiederholungsprotokolle |

### <a name="key-considerations"></a>Wichtige Aspekte
- /usr/sap/SID ist eine symbolische Verknüpfung mit /hana/shared/SID.

## <a name="single-node-mcos"></a>Einzelner Knoten MCOS

Diese Topologie unterstützt einen Knoten in einer Konfiguration mit zentraler Skalierung und mehreren SIDs.

### <a name="architecture-diagram"></a>Architekturdiagramm  

![Einzelner Knoten MCOS](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
Die folgenden Netzwerkschnittstellen sind vorkonfiguriert:

| Logische Netzwerkkarten-Schnittstelle | SKU-Typ | Name mit dem Betriebssystem SUSE | Name mit dem Betriebssystem RHEL | Anwendungsfall|
| --- | --- | --- | --- | --- |
| Ein | TYP I | eth0.tenant | eno1.tenant | Client zu HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Konfiguriert, aber nicht verwendet |
| C | TYP I | eth1.tenant | eno2.tenant | Knoten zu Speicher |
| D | TYP I | eth4.tenant | eno4.tenant | Konfiguriert, aber nicht verwendet |
| Ein | TYP II | vlan\<tenantNo> | team0.tenant | Client zu HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Konfiguriert, aber nicht verwendet |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Knoten zu Speicher |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfiguriert, aber nicht verwendet |

### <a name="storage"></a>Storage
Die folgenden Bereitstellungspunkte sind vorkonfiguriert:

| Mountpunkt | Anwendungsfall | 
| --- | --- |
|/hana/shared/SID1 | HANA-Installation für SID1 | 
|/hana/data/SID1/mnt00001 | Installation der Datendateien für SID1 | 
|/hana/log/SID1/mnt00001 | Installation der Protokolldateien für SID1 | 
|/hana/logbackups/SID1 | Wiederholungsprotokolle für SID1 |
|/hana/shared/SID2 | HANA-Installation für SID2 | 
|/hana/data/SID2/mnt00001 | Installation der Datendateien für SID2 | 
|/hana/log/SID2/mnt00001 | Installation der Protokolldateien für SID2 | 
|/hana/logbackups/SID2 | Wiederholungsprotokolle für SID2 |

### <a name="key-considerations"></a>Wichtige Aspekte
- /usr/sap/SID ist eine symbolische Verknüpfung mit /hana/shared/SID.
- Die Verteilung der Volumegröße basiert auf der Datenbankgröße im Arbeitsspeicher. Im Abschnitt [Übersicht und Architektur](./hana-overview-architecture.md) erfahren Sie, welche Datenbankgrößen im Arbeitsspeicher in einer Umgebung mit mehreren SIDs unterstützt werden.

## <a name="single-node-with-dr-using-storage-replication"></a>Einzelner Knoten mit Notfallwiederherstellung mithilfe der Speicherreplikation
 
Diese Topologie unterstützt einen Knoten in einer Konfiguration mit zentraler Skalierung und einer SID oder mehreren SIDs, mit speicherbasierter Replikation in den DR-Standort für eine primäre SID. Im Diagramm ist nur ein System mit einer einzigen SID dargestellt, MCOS-Systeme werden aber ebenfalls unterstützt.

### <a name="architecture-diagram"></a>Architekturdiagramm  

![Einzelner Knoten mit Notfallwiederherstellung mithilfe der Speicherreplikation](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
Die folgenden Netzwerkschnittstellen sind vorkonfiguriert:

| Logische Netzwerkkarten-Schnittstelle | SKU-Typ | Name mit dem Betriebssystem SUSE | Name mit dem Betriebssystem RHEL | Anwendungsfall|
| --- | --- | --- | --- | --- |
| Ein | TYP I | eth0.tenant | eno1.tenant | Client zu HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Konfiguriert, aber nicht verwendet |
| C | TYP I | eth1.tenant | eno2.tenant | Knoten zu Speicher |
| D | TYP I | eth4.tenant | eno4.tenant | Konfiguriert, aber nicht verwendet |
| Ein | TYP II | vlan\<tenantNo> | team0.tenant | Client zu HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Konfiguriert, aber nicht verwendet |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Knoten zu Speicher |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfiguriert, aber nicht verwendet |

### <a name="storage"></a>Storage
Die folgenden Bereitstellungspunkte sind vorkonfiguriert:

| Mountpunkt | Anwendungsfall | 
| --- | --- |
|/hana/shared/SID | HANA-Installation für SID | 
|/hana/data/SID/mnt00001 | Installation der Datendateien für SID | 
|/hana/log/SID/mnt00001 | Installation der Protokolldateien für SID | 
|/hana/logbackups/SID | Wiederholungsprotokolle für SID |


### <a name="key-considerations"></a>Wichtige Aspekte
- /usr/sap/SID ist eine symbolische Verknüpfung mit /hana/shared/SID.
- Für MCOS: Die Verteilung der Volumegröße basiert auf der Datenbankgröße im Arbeitsspeicher. Im Abschnitt [Übersicht und Architektur](./hana-overview-architecture.md) erfahren Sie, welche Datenbankgrößen im Arbeitsspeicher in einer Umgebung mit mehreren SIDs unterstützt werden.
- Am DR-Standort: Die Volumes und Bereitstellungspunkte sind für die Installation der HANA-Produktionsinstanz auf der DR-HLI-Einheit konfiguriert (als „Für HANA-Installation erforderlich“ markiert). 
- Am DR-Standort: Die Daten, Protokollsicherungen und freigegebenen Volumes (als „Speicherreplikation“ markiert) werden über eine Momentaufnahme vom Produktionsstandort repliziert. Diese Volumes werden nur während eines Failovers bereitgestellt. Weitere Informationen finden Sie unter [Failoverprozedur für die Notfallwiederherstellung](./hana-overview-high-availability-disaster-recovery.md).
- Das Startvolume für die *SKU-Typ I-Klasse* wird auf dem DR-Knoten repliziert.


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>Einzelner Knoten mit Notfallwiederherstellung (Mehrzweck) unter Verwendung der Speicherreplikation
 
Diese Topologie unterstützt einen Knoten in einer Konfiguration mit zentraler Skalierung und einer SID oder mehreren SIDs, mit speicherbasierter Replikation in den DR-Standort für eine primäre SID. Im Diagramm ist nur ein System mit einer einzigen SID dargestellt, MCOS-Systeme (mit mehreren SIDs) werden aber ebenfalls unterstützt. Am DR-Standort wird die HLI-Einheit für die QA-Instanz verwendet, während die Produktionsvorgänge vom primären Standort aus ausgeführt werden. Während des DR-Failovers (oder der Failovertests) wird die QA-Instanz am DR-Standort außer Betrieb genommen.

### <a name="architecture-diagram"></a>Architekturdiagramm  

![Einzelner Knoten mit Notfallwiederherstellung (Mehrzweck) unter Verwendung der Speicherreplikation](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
Die folgenden Netzwerkschnittstellen sind vorkonfiguriert:

| Logische Netzwerkkarten-Schnittstelle | SKU-Typ | Name mit dem Betriebssystem SUSE | Name mit dem Betriebssystem RHEL | Anwendungsfall|
| --- | --- | --- | --- | --- |
| Ein | TYP I | eth0.tenant | eno1.tenant | Client zu HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Konfiguriert, aber nicht verwendet |
| C | TYP I | eth1.tenant | eno2.tenant | Knoten zu Speicher |
| D | TYP I | eth4.tenant | eno4.tenant | Konfiguriert, aber nicht verwendet |
| Ein | TYP II | vlan\<tenantNo> | team0.tenant | Client zu HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Konfiguriert, aber nicht verwendet |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Knoten zu Speicher |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfiguriert, aber nicht verwendet |

### <a name="storage"></a>Storage
Die folgenden Bereitstellungspunkte sind vorkonfiguriert:

| Mountpunkt | Anwendungsfall | 
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
|/hana/shared/QA-SID | SID der HANA-Installation für QA | 
|/hana/data/QA-SID/mnt00001 | SID der Datendateieninstallation für QA | 
|/hana/log/QA-SID/mnt00001 | SID der Protokolldateieninstallation für QA |
|/hana/logbackups/QA-SID | Wiederholungsprotokolle für QA-SID |

### <a name="key-considerations"></a>Wichtige Aspekte
- /usr/sap/SID ist eine symbolische Verknüpfung mit /hana/shared/SID.
- Für MCOS: Die Verteilung der Volumegröße basiert auf der Datenbankgröße im Arbeitsspeicher. Im Abschnitt [Übersicht und Architektur](./hana-overview-architecture.md) erfahren Sie, welche Datenbankgrößen im Arbeitsspeicher in einer Umgebung mit mehreren SIDs unterstützt werden.
- Am DR-Standort: Die Volumes und Bereitstellungspunkte sind für die Installation der HANA-Produktionsinstanz auf der DR-HLI-Einheit konfiguriert (als „Für HANA-Installation erforderlich“ markiert). 
- Am DR-Standort: Die Daten, Protokollsicherungen und freigegebenen Volumes (als „Speicherreplikation“ markiert) werden über eine Momentaufnahme vom Produktionsstandort repliziert. Diese Volumes werden nur während eines Failovers bereitgestellt. Weitere Informationen finden Sie unter [Failoverprozedur für die Notfallwiederherstellung](./hana-overview-high-availability-disaster-recovery.md). 
- Am DR-Standort: Die Daten, Protokollsicherungen, Protokolle und freigegebenen Volumes für QA (als „QA-Instanzinstallation“ gekennzeichnet) sind für die Installation der QA-Instanz konfiguriert.
- Das Startvolume für die *SKU-Typ I-Klasse* wird auf dem DR-Knoten repliziert.

## <a name="hsr-with-stonith-for-high-availability"></a>HSR mit STONITH für Hochverfügbarkeit
 
Diese Topologie unterstützt zwei Knoten für die Konfiguration der HANA-Systemreplikation. Diese Konfiguration wird nur für einzelne HANA-Instanzen auf einem Knoten unterstützt. Das bedeutet, dass MCOS-Szenarien *nicht* unterstützt werden.

> [!NOTE]
> Ab Dezember 2019 wird diese Architektur nur für das Betriebssystem SUSE unterstützt.


### <a name="architecture-diagram"></a>Architekturdiagramm  

![HSR mit STONITH für Hochverfügbarkeit](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
Die folgenden Netzwerkschnittstellen sind vorkonfiguriert:

| Logische Netzwerkkarten-Schnittstelle | SKU-Typ | Name mit dem Betriebssystem SUSE | Name mit dem Betriebssystem RHEL | Anwendungsfall|
| --- | --- | --- | --- | --- |
| Ein | TYP I | eth0.tenant | eno1.tenant | Client zu HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Konfiguriert, aber nicht verwendet |
| C | TYP I | eth1.tenant | eno2.tenant | Knoten zu Speicher |
| D | TYP I | eth4.tenant | eno4.tenant | Verwendet für STONITH |
| Ein | TYP II | vlan\<tenantNo> | team0.tenant | Client zu HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Konfiguriert, aber nicht verwendet |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Knoten zu Speicher |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Verwendet für STONITH |

### <a name="storage"></a>Storage
Die folgenden Bereitstellungspunkte sind vorkonfiguriert:

| Mountpunkt | Anwendungsfall | 
| --- | --- |
|**Auf dem primären Knoten**|
|/hana/shared/SID | SID der HANA-Installation für die Produktion | 
|/hana/data/SID/mnt00001 | SID der Datendateieninstallation für die Produktion | 
|/hana/log/SID/mnt00001 | SID der Protokolldateieninstallation für die Produktion | 
|/hana/logbackups/SID | SID für die Wiederholungsprotokolle für die Produktion |
|**Auf dem sekundären Knoten**|
|/hana/shared/SID | SID der HANA-Installation für den sekundären Knoten | 
|/hana/data/SID/mnt00001 | SID der Datendateieninstallation für den sekundären Knoten | 
|/hana/log/SID/mnt00001 | SID der Protokolldateieninstallation für den sekundären Knoten | 
|/hana/logbackups/SID | SID der Wiederholungsprotokolle für sekundären Knoten |

### <a name="key-considerations"></a>Wichtige Aspekte
- /usr/sap/SID ist eine symbolische Verknüpfung mit /hana/shared/SID.
- Für MCOS: Die Verteilung der Volumegröße basiert auf der Datenbankgröße im Arbeitsspeicher. Im Abschnitt [Übersicht und Architektur](./hana-overview-architecture.md) erfahren Sie, welche Datenbankgrößen im Arbeitsspeicher in einer Umgebung mit mehreren SIDs unterstützt werden.
- STONITH: Eine SBD wird für das STONITH-Setup konfiguriert. Die Verwendung von STONITH ist jedoch optional.


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>Hochverfügbarkeit mit HSR und Notfallwiederherstellung mit Speicherreplikation
 
Diese Topologie unterstützt zwei Knoten für die Konfiguration der HANA-Systemreplikation. Sowohl normale als auch Mehrzweck-DR wird unterstützt. Diese Konfigurationen werden nur für einzelne HANA-Instanzen auf einem Knoten unterstützt. Das bedeutet, das MCOS-Szenarien mit diesen Konfigurationen *nicht* unterstützt werden.

Im Diagramm ist ein Mehrzweckszenario am DR-Standort dargestellt. Die HLI-Einheit wird für die QA-Instanz verwendet, während die Produktionsvorgänge vom primären Standort aus ausgeführt werden. Während des DR-Failovers (oder der Failovertests) wird die QA-Instanz am DR-Standort außer Betrieb genommen. 

### <a name="architecture-diagram"></a>Architekturdiagramm  

![Hochverfügbarkeit mit HSR und Notfallwiederherstellung mit Speicherreplikation](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
Die folgenden Netzwerkschnittstellen sind vorkonfiguriert:

| Logische Netzwerkkarten-Schnittstelle | SKU-Typ | Name mit dem Betriebssystem SUSE | Name mit dem Betriebssystem RHEL | Anwendungsfall|
| --- | --- | --- | --- | --- |
| Ein | TYP I | eth0.tenant | eno1.tenant | Client zu HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Konfiguriert, aber nicht verwendet |
| C | TYP I | eth1.tenant | eno2.tenant | Knoten zu Speicher |
| D | TYP I | eth4.tenant | eno4.tenant | Verwendet für STONITH |
| Ein | TYP II | vlan\<tenantNo> | team0.tenant | Client zu HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Konfiguriert, aber nicht verwendet |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Knoten zu Speicher |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Verwendet für STONITH |

### <a name="storage"></a>Storage
Die folgenden Bereitstellungspunkte sind vorkonfiguriert:

| Mountpunkt | Anwendungsfall | 
| --- | --- |
|**Auf dem primären Knoten am primären Standort**|
|/hana/shared/SID | SID der HANA-Installation für die Produktion | 
|/hana/data/SID/mnt00001 | SID der Datendateieninstallation für die Produktion | 
|/hana/log/SID/mnt00001 | SID der Protokolldateieninstallation für die Produktion | 
|/hana/logbackups/SID | SID für die Wiederholungsprotokolle für die Produktion |
|**Auf dem sekundären Knoten am primären Standort**|
|/hana/shared/SID | SID der HANA-Installation für den sekundären Knoten | 
|/hana/data/SID/mnt00001 | SID der Datendateieninstallation für den sekundären Knoten | 
|/hana/log/SID/mnt00001 | SID der Protokolldateieninstallation für den sekundären Knoten | 
|/hana/logbackups/SID | SID der Wiederholungsprotokolle für sekundären Knoten |
|**Am DR-Standort**|
|/hana/shared/SID | SID der HANA-Installation für die Produktion | 
|/hana/data/SID/mnt00001 | SID der Datendateieninstallation für die Produktion | 
|/hana/log/SID/mnt00001 | SID der Protokolldateieninstallation für die Produktion | 
|/hana/shared/QA-SID | SID der HANA-Installation für QA | 
|/hana/data/QA-SID/mnt00001 | SID der Datendateieninstallation für QA | 
|/hana/log/QA-SID/mnt00001 | SID der Protokolldateieninstallation für QA |
|/hana/logbackups/QA-SID | Wiederholungsprotokolle für QA-SID |

### <a name="key-considerations"></a>Wichtige Aspekte
- /usr/sap/SID ist eine symbolische Verknüpfung mit /hana/shared/SID.
- Für MCOS: Die Verteilung der Volumegröße basiert auf der Datenbankgröße im Arbeitsspeicher. Im Abschnitt [Übersicht und Architektur](./hana-overview-architecture.md) erfahren Sie, welche Datenbankgrößen im Arbeitsspeicher in einer Umgebung mit mehreren SIDs unterstützt werden.
- STONITH: Eine SBD wird für das STONITH-Setup konfiguriert. Die Verwendung von STONITH ist jedoch optional.
- Am DR-Standort: *Zwei Sätze von Speichervolumes* sind für die Replikation des primären und sekundären Knotens erforderlich.
- Am DR-Standort: Die Volumes und Bereitstellungspunkte sind für die Installation der HANA-Produktionsinstanz auf der DR-HLI-Einheit konfiguriert (als „Für HANA-Installation erforderlich“ markiert). 
- Am DR-Standort: Die Daten, Protokollsicherungen und freigegebenen Volumes (als „Speicherreplikation“ markiert) werden über eine Momentaufnahme vom Produktionsstandort repliziert. Diese Volumes werden nur während eines Failovers bereitgestellt. Weitere Informationen finden Sie unter [Failoverprozedur für die Notfallwiederherstellung](./hana-overview-high-availability-disaster-recovery.md). 
- Am DR-Standort: Die Daten, Protokollsicherungen, Protokolle und freigegebenen Volumes für QA (als „QA-Instanzinstallation“ gekennzeichnet) sind für die Installation der QA-Instanz konfiguriert.
- Das Startvolume für die *SKU-Typ I-Klasse* wird auf dem DR-Knoten repliziert.


## <a name="host-auto-failover-11"></a>Automatisches Hostfailover (1+1)
 
Diese Topologie unterstützt zwei Knoten in einer automatischen Hostfailoverkonfiguration. Auf einem Knoten wird die Master-/Workerrolle ausgeführt, der andere fungiert als Standbyknoten. *SAP unterstützt dieses Szenario nur für S/4 HANA.* Weitere Informationen finden Sie im OSS-Hinweis [2408419 – SAP S/4HANA: Unterstützung für mehrere Knoten](https://launchpad.support.sap.com/#/notes/2408419).



### <a name="architecture-diagram"></a>Architekturdiagramm  

![Automatisches Hostfailover (1+1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
Die folgenden Netzwerkschnittstellen sind vorkonfiguriert:

| Logische Netzwerkkarten-Schnittstelle | SKU-Typ | Name mit dem Betriebssystem SUSE | Name mit dem Betriebssystem RHEL | Anwendungsfall|
| --- | --- | --- | --- | --- |
| Ein | TYP I | eth0.tenant | eno1.tenant | Client zu HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Kommunikation zwischen Knoten |
| C | TYP I | eth1.tenant | eno2.tenant | Knoten zu Speicher |
| D | TYP I | eth4.tenant | eno4.tenant | Konfiguriert, aber nicht verwendet |
| Ein | TYP II | vlan\<tenantNo> | team0.tenant | Client zu HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Kommunikation zwischen Knoten |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Knoten zu Speicher |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfiguriert, aber nicht verwendet |

### <a name="storage"></a>Storage
Die folgenden Bereitstellungspunkte sind vorkonfiguriert:

| Mountpunkt | Anwendungsfall | 
| --- | --- |
|**Auf den Master- und Standbyknoten**|
|/hana/shared | SID der HANA-Installation für die Produktion | 
|/hana/data/SID/mnt00001 | SID der Datendateieninstallation für die Produktion | 
|/hana/log/SID/mnt00001 | SID der Protokolldateieninstallation für die Produktion | 
|/hana/logbackups/SID | SID für die Wiederholungsprotokolle für die Produktion |



### <a name="key-considerations"></a>Wichtige Aspekte
- /usr/sap/SID ist eine symbolische Verknüpfung mit /hana/shared/SID.
- Auf dem Standbyknoten: Die Volumes und Bereitstellungspunkte sind für die Installation der HANA-Instanz auf der Standbyeinheit konfiguriert (als „Für HANA-Installation erforderlich“ markiert).
 

## <a name="scale-out-with-standby"></a>Horizontales Hochskalieren mit Standby
 
Diese Topologie unterstützt mehrere Knoten in einer horizontal hochskalierten Konfiguration. Auf einem Knoten wird die Masterrolle ausgeführt, auf mindestens einem Knoten wird eine Workerrolle ausgeführt, und mindestens ein Knoten fungiert als Standbyknoten. Allerdings kann es zu jedem Zeitpunkt immer nur einen Masterknoten geben.


### <a name="architecture-diagram"></a>Architekturdiagramm  

![Horizontales Hochskalieren mit Standby](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
Die folgenden Netzwerkschnittstellen sind vorkonfiguriert:

| Logische Netzwerkkarten-Schnittstelle | SKU-Typ | Name mit dem Betriebssystem SUSE | Name mit dem Betriebssystem RHEL | Anwendungsfall|
| --- | --- | --- | --- | --- |
| Ein | TYP I | eth0.tenant | eno1.tenant | Client zu HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Kommunikation zwischen Knoten |
| C | TYP I | eth1.tenant | eno2.tenant | Knoten zu Speicher |
| D | TYP I | eth4.tenant | eno4.tenant | Konfiguriert, aber nicht verwendet |
| Ein | TYP II | vlan\<tenantNo> | team0.tenant | Client zu HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Kommunikation zwischen Knoten |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Knoten zu Speicher |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfiguriert, aber nicht verwendet |

### <a name="storage"></a>Storage
Die folgenden Bereitstellungspunkte sind vorkonfiguriert:

| Mountpunkt | Anwendungsfall | 
| --- | --- |
|**Auf den Master-, Worker- und Standbyknoten**|
|/hana/shared | SID der HANA-Installation für die Produktion | 
|/hana/data/SID/mnt00001 | SID der Datendateieninstallation für die Produktion | 
|/hana/log/SID/mnt00001 | SID der Protokolldateieninstallation für die Produktion | 
|/hana/logbackups/SID | SID für die Wiederholungsprotokolle für die Produktion |


## <a name="scale-out-without-standby"></a>Horizontales Hochskalieren ohne Standby
 
Diese Topologie unterstützt mehrere Knoten in einer horizontal hochskalierten Konfiguration. Auf einem Knoten wird die Masterrolle ausgeführt, und auf mindestens einem Knoten wird eine Workerrolle ausgeführt. Allerdings kann es zu jedem Zeitpunkt immer nur einen Masterknoten geben.


### <a name="architecture-diagram"></a>Architekturdiagramm  

![Horizontales Hochskalieren ohne Standby](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
Die folgenden Netzwerkschnittstellen sind vorkonfiguriert:

| Logische Netzwerkkarten-Schnittstelle | SKU-Typ | Name mit dem Betriebssystem SUSE | Name mit dem Betriebssystem RHEL | Anwendungsfall|
| --- | --- | --- | --- | --- |
| Ein | TYP I | eth0.tenant | eno1.tenant | Client zu HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Kommunikation zwischen Knoten |
| C | TYP I | eth1.tenant | eno2.tenant | Knoten zu Speicher |
| D | TYP I | eth4.tenant | eno4.tenant | Konfiguriert, aber nicht verwendet |
| Ein | TYP II | vlan\<tenantNo> | team0.tenant | Client zu HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Kommunikation zwischen Knoten |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Knoten zu Speicher |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfiguriert, aber nicht verwendet |

### <a name="storage"></a>Storage
Die folgenden Bereitstellungspunkte sind vorkonfiguriert:

| Mountpunkt | Anwendungsfall | 
| --- | --- |
|**Auf den Master- und Workerknoten**|
|/hana/shared | SID der HANA-Installation für die Produktion | 
|/hana/data/SID/mnt00001 | SID der Datendateieninstallation für die Produktion | 
|/hana/log/SID/mnt00001 | SID der Protokolldateieninstallation für die Produktion | 
|/hana/logbackups/SID | SID für die Wiederholungsprotokolle für die Produktion |


### <a name="key-considerations"></a>Wichtige Aspekte
- /usr/sap/SID ist eine symbolische Verknüpfung mit /hana/shared/SID.

## <a name="scale-out-with-dr-using-storage-replication"></a>Horizontale Skalierung mit Notfallwiederherstellung mithilfe der Speicherreplikation
 
Diese Topologie unterstützt mehrere Knoten in einer horizontal hochskalierten Konfiguration mit einer DR. Sowohl normale als auch Mehrzweck-DR wird unterstützt. In der Abbildung wird nur eine DR mit einem einzigen Zweck dargestellt. Sie können diese Topologie mit oder ohne Standbyknoten anfordern.


### <a name="architecture-diagram"></a>Architekturdiagramm  

![Horizontale Skalierung mit Notfallwiederherstellung mithilfe der Speicherreplikation](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
Die folgenden Netzwerkschnittstellen sind vorkonfiguriert:

| Logische Netzwerkkarten-Schnittstelle | SKU-Typ | Name mit dem Betriebssystem SUSE | Name mit dem Betriebssystem RHEL | Anwendungsfall|
| --- | --- | --- | --- | --- |
| Ein | TYP I | eth0.tenant | eno1.tenant | Client zu HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Kommunikation zwischen Knoten |
| C | TYP I | eth1.tenant | eno2.tenant | Knoten zu Speicher |
| D | TYP I | eth4.tenant | eno4.tenant | Konfiguriert, aber nicht verwendet |
| Ein | TYP II | vlan\<tenantNo> | team0.tenant | Client zu HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Kommunikation zwischen Knoten |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Knoten zu Speicher |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfiguriert, aber nicht verwendet |

### <a name="storage"></a>Storage
Die folgenden Bereitstellungspunkte sind vorkonfiguriert:

| Mountpunkt | Anwendungsfall | 
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
-  Am DR-Standort: Die Volumes und Bereitstellungspunkte sind für die Installation der HANA-Produktionsinstanz auf der DR-HLI-Einheit konfiguriert (als „Für HANA-Installation erforderlich“ markiert). 
- Am DR-Standort: Die Daten, Protokollsicherungen und freigegebenen Volumes (als „Speicherreplikation“ markiert) werden über eine Momentaufnahme vom Produktionsstandort repliziert. Diese Volumes werden nur während eines Failovers bereitgestellt. Weitere Informationen finden Sie unter [Failoverprozedur für die Notfallwiederherstellung](./hana-overview-high-availability-disaster-recovery.md). 
- Das Startvolume für die *SKU-Typ I-Klasse* wird auf dem DR-Knoten repliziert.


## <a name="single-node-with-dr-using-hsr"></a>Einzelner Knoten mit Notfallwiederherstellung mithilfe von HSR
 
Diese Topologie unterstützt einen Knoten in einer Konfiguration mit zentraler Skalierung und einer SID. Die HANA-Systemreplikation erfolgt zum DR-Standort für eine primäre SID. Im Diagramm ist nur ein System mit einer einzigen SID dargestellt, MCOS-Systeme (mit mehreren SIDs) werden aber ebenfalls unterstützt.

### <a name="architecture-diagram"></a>Architekturdiagramm  

![Einzelner Knoten mit Notfallwiederherstellung mithilfe von HSR](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>Ethernet
Die folgenden Netzwerkschnittstellen sind vorkonfiguriert:

| Logische Netzwerkkarten-Schnittstelle | SKU-Typ | Name mit dem Betriebssystem SUSE | Name mit dem Betriebssystem RHEL | Anwendungsfall|
| --- | --- | --- | --- | --- |
| Ein | TYP I | eth0.tenant | eno1.tenant | Client zu HLI/HSR |
| B | TYP I | eth2.tenant | eno3.tenant | Konfiguriert, aber nicht verwendet |
| C | TYP I | eth1.tenant | eno2.tenant | Knoten zu Speicher |
| D | TYP I | eth4.tenant | eno4.tenant | Konfiguriert, aber nicht verwendet |
| Ein | TYP II | vlan\<tenantNo> | team0.tenant | Client zu HLI/HSR |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Konfiguriert, aber nicht verwendet |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Knoten zu Speicher |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfiguriert, aber nicht verwendet |

### <a name="storage"></a>Storage
Die folgenden Bereitstellungspunkte sind auf beiden HLI-Einheiten (primär und DR) vorkonfiguriert:

| Mountpunkt | Anwendungsfall | 
| --- | --- |
|/hana/shared/SID | HANA-Installation für SID | 
|/hana/data/SID/mnt00001 | Installation der Datendateien für SID | 
|/hana/log/SID/mnt00001 | Installation der Protokolldateien für SID | 
|/hana/logbackups/SID | Wiederholungsprotokolle für SID |


### <a name="key-considerations"></a>Wichtige Aspekte
- /usr/sap/SID ist eine symbolische Verknüpfung mit /hana/shared/SID.
- Für MCOS: Die Verteilung der Volumegröße basiert auf der Datenbankgröße im Arbeitsspeicher. Im Abschnitt [Übersicht und Architektur](./hana-overview-architecture.md) erfahren Sie, welche Datenbankgrößen im Arbeitsspeicher in einer Umgebung mit mehreren SIDs unterstützt werden.
- Der primäre Knoten führt über die HANA-Systemreplikation eine Synchronisierung mit dem DR-Knoten durch. 
- Mit [Global Reach](../../../expressroute/expressroute-global-reach.md) werden die ExpressRoute-Leitungen miteinander verbunden, um ein privates Netzwerk zwischen Ihren regionalen Netzwerken aufzubauen.



## <a name="single-node-hsr-to-dr-cost-optimized"></a>HSR in einem einzelnen Knoten für Notfallwiederherstellung (kostenoptimiert) 
 
 Diese Topologie unterstützt einen Knoten in einer Konfiguration mit zentraler Skalierung und einer SID. Die HANA-Systemreplikation erfolgt zum DR-Standort für eine primäre SID. Im Diagramm ist nur ein System mit einer einzigen SID dargestellt, MCOS-Systeme (mit mehreren SIDs) werden aber ebenfalls unterstützt. Am DR-Standort wird eine HLI-Einheit für die QA-Instanz verwendet, während die Produktionsvorgänge vom primären Standort aus ausgeführt werden. Während des DR-Failovers (oder der Failovertests) wird die QA-Instanz am DR-Standort außer Betrieb genommen.

### <a name="architecture-diagram"></a>Architekturdiagramm  

![HSR in einem einzelnen Knoten für Notfallwiederherstellung (kostenoptimiert)](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>Ethernet
Die folgenden Netzwerkschnittstellen sind vorkonfiguriert:

| Logische Netzwerkkarten-Schnittstelle | SKU-Typ | Name mit dem Betriebssystem SUSE | Name mit dem Betriebssystem RHEL | Anwendungsfall|
| --- | --- | --- | --- | --- |
| Ein | TYP I | eth0.tenant | eno1.tenant | Client zu HLI/HSR |
| B | TYP I | eth2.tenant | eno3.tenant | Konfiguriert, aber nicht verwendet |
| C | TYP I | eth1.tenant | eno2.tenant | Knoten zu Speicher |
| D | TYP I | eth4.tenant | eno4.tenant | Konfiguriert, aber nicht verwendet |
| Ein | TYP II | vlan\<tenantNo> | team0.tenant | Client zu HLI/HSR |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Konfiguriert, aber nicht verwendet |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Knoten zu Speicher |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfiguriert, aber nicht verwendet |

### <a name="storage"></a>Storage
Die folgenden Bereitstellungspunkte sind vorkonfiguriert:

| Mountpunkt | Anwendungsfall | 
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
|/hana/logbackups/SID | SID für die Wiederholungsprotokolle für die Produktion |
|/hana/shared/QA-SID | SID der HANA-Installation für QA | 
|/hana/data/QA-SID/mnt00001 | SID der Datendateieninstallation für QA | 
|/hana/log/QA-SID/mnt00001 | SID der Protokolldateieninstallation für QA |
|/hana/logbackups/QA-SID | Wiederholungsprotokolle für QA-SID |

### <a name="key-considerations"></a>Wichtige Aspekte
- /usr/sap/SID ist eine symbolische Verknüpfung mit /hana/shared/SID.
- Für MCOS: Die Verteilung der Volumegröße basiert auf der Datenbankgröße im Arbeitsspeicher. Im Abschnitt [Übersicht und Architektur](./hana-overview-architecture.md) erfahren Sie, welche Datenbankgrößen im Arbeitsspeicher in einer Umgebung mit mehreren SIDs unterstützt werden.
- Am DR-Standort: Die Volumes und Bereitstellungspunkte sind für die Installation der HANA-Produktionsinstanz auf der HLI-Einheit für die Notfallwiederherstellung konfiguriert (als „PROD-Instanz am DR-Standort“ markiert). 
- Am DR-Standort: Die Daten, Protokollsicherungen, Protokolle und freigegebenen Volumes für QA (als „QA-Instanzinstallation“ gekennzeichnet) sind für die Installation der QA-Instanz konfiguriert.
- Der primäre Knoten führt über die HANA-Systemreplikation eine Synchronisierung mit dem DR-Knoten durch. 
- Mit [Global Reach](../../../expressroute/expressroute-global-reach.md) werden die ExpressRoute-Leitungen miteinander verbunden, um ein privates Netzwerk zwischen Ihren regionalen Netzwerken aufzubauen.

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>Hochverfügbarkeit und Notfallwiederherstellung mit HSR 
 
 Diese Topologie unterstützt zwei Knoten für die Konfiguration der HANA-Systemreplikation, um für die Hochverfügbarkeit der lokalen Regionen zu sorgen. Für die Notfallwiederherstellung wird der dritte Knoten in der DR-Region mithilfe von HSR (asynchroner Modus) mit dem primären Standort synchronisiert. 

### <a name="architecture-diagram"></a>Architekturdiagramm  

![Hochverfügbarkeit und Notfallwiederherstellung mit HSR](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>Ethernet
Die folgenden Netzwerkschnittstellen sind vorkonfiguriert:

| Logische Netzwerkkarten-Schnittstelle | SKU-Typ | Name mit dem Betriebssystem SUSE | Name mit dem Betriebssystem RHEL | Anwendungsfall|
| --- | --- | --- | --- | --- |
| Ein | TYP I | eth0.tenant | eno1.tenant | Client zu HLI/HSR |
| B | TYP I | eth2.tenant | eno3.tenant | Konfiguriert, aber nicht verwendet |
| C | TYP I | eth1.tenant | eno2.tenant | Knoten zu Speicher |
| D | TYP I | eth4.tenant | eno4.tenant | Konfiguriert, aber nicht verwendet |
| Ein | TYP II | vlan\<tenantNo> | team0.tenant | Client zu HLI/HSR |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Konfiguriert, aber nicht verwendet |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Knoten zu Speicher |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfiguriert, aber nicht verwendet |

### <a name="storage"></a>Storage
Die folgenden Bereitstellungspunkte sind vorkonfiguriert:

| Mountpunkt | Anwendungsfall | 
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
|/hana/logbackups/SID | SID für die Wiederholungsprotokolle für die Produktion |


### <a name="key-considerations"></a>Wichtige Aspekte
- /usr/sap/SID ist eine symbolische Verknüpfung mit /hana/shared/SID.
- Am DR-Standort: Die Volumes und Bereitstellungspunkte sind für die Installation der HANA-Produktionsinstanz auf der HLI-Einheit für die Notfallwiederherstellung konfiguriert (als „PROD-DR-Instanz“ markiert). 
- Der primäre Standortknoten führt über die HANA-Systemreplikation eine Synchronisierung mit dem DR-Knoten durch. 
- Mit [Global Reach](../../../expressroute/expressroute-global-reach.md) werden die ExpressRoute-Leitungen miteinander verbunden, um ein privates Netzwerk zwischen Ihren regionalen Netzwerken aufzubauen.

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>Hochverfügbarkeit und Notfallwiederherstellung mit HSR (kostenoptimiert)
 
 Diese Topologie unterstützt zwei Knoten für die Konfiguration der HANA-Systemreplikation, um für die Hochverfügbarkeit der lokalen Regionen zu sorgen. Für die Notfallwiederherstellung wird der dritte Knoten in der DR-Region mithilfe von HSR (asynchroner Modus) mit dem primären Standort synchronisiert, während eine weitere Instanz (z. B. QA) bereits außerhalb des DR-Knotens ausgeführt wird. 

### <a name="architecture-diagram"></a>Architekturdiagramm  

![Hochverfügbarkeit und Notfallwiederherstellung mit HSR (kostenoptimiert)](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>Ethernet
Die folgenden Netzwerkschnittstellen sind vorkonfiguriert:

| Logische Netzwerkkarten-Schnittstelle | SKU-Typ | Name mit dem Betriebssystem SUSE | Name mit dem Betriebssystem RHEL | Anwendungsfall|
| --- | --- | --- | --- | --- |
| Ein | TYP I | eth0.tenant | eno1.tenant | Client zu HLI/HSR |
| B | TYP I | eth2.tenant | eno3.tenant | Konfiguriert, aber nicht verwendet |
| C | TYP I | eth1.tenant | eno2.tenant | Knoten zu Speicher |
| D | TYP I | eth4.tenant | eno4.tenant | Konfiguriert, aber nicht verwendet |
| Ein | TYP II | vlan\<tenantNo> | team0.tenant | Client zu HLI/HSR |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Konfiguriert, aber nicht verwendet |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Knoten zu Speicher |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfiguriert, aber nicht verwendet |

### <a name="storage"></a>Storage
Die folgenden Bereitstellungspunkte sind vorkonfiguriert:

| Mountpunkt | Anwendungsfall | 
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
|/hana/logbackups/SID | SID für die Wiederholungsprotokolle für die Produktion |
|/hana/shared/QA-SID | SID der HANA-Installation für QA | 
|/hana/data/QA-SID/mnt00001 | SID der Datendateieninstallation für QA | 
|/hana/log/QA-SID/mnt00001 | SID der Protokolldateieninstallation für QA |
|/hana/logbackups/QA-SID | Wiederholungsprotokolle für QA-SID |

### <a name="key-considerations"></a>Wichtige Aspekte
- /usr/sap/SID ist eine symbolische Verknüpfung mit /hana/shared/SID.
- Am DR-Standort: Die Volumes und Bereitstellungspunkte sind für die Installation der HANA-Produktionsinstanz auf der HLI-Einheit für die Notfallwiederherstellung konfiguriert (als „PROD-DR-Instanz“ markiert). 
- Am DR-Standort: Die Daten, Protokollsicherungen, Protokolle und freigegebenen Volumes für QA (als „QA-Instanzinstallation“ gekennzeichnet) sind für die Installation der QA-Instanz konfiguriert.
- Der primäre Standortknoten führt über die HANA-Systemreplikation eine Synchronisierung mit dem DR-Knoten durch. 
- Mit [Global Reach](../../../expressroute/expressroute-global-reach.md) werden die ExpressRoute-Leitungen miteinander verbunden, um ein privates Netzwerk zwischen Ihren regionalen Netzwerken aufzubauen.

## <a name="scale-out-with-dr-using-hsr"></a>Horizontales Hochskalieren mit DR über HSR
 
Diese Topologie unterstützt mehrere Knoten in einer horizontal hochskalierten Konfiguration mit einer DR. Sie können diese Topologie mit oder ohne Standbyknoten anfordern. Der primäre Standortknoten führt über die HANA-Systemreplikation (asynchroner Modus) eine Synchronisierung mit dem DR-Standortknoten durch.


### <a name="architecture-diagram"></a>Architekturdiagramm  

[ ![Horizontale Skalierung mit DR über HSR](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>Ethernet
Die folgenden Netzwerkschnittstellen sind vorkonfiguriert:

| Logische Netzwerkkarten-Schnittstelle | SKU-Typ | Name mit dem Betriebssystem SUSE | Name mit dem Betriebssystem RHEL | Anwendungsfall|
| --- | --- | --- | --- | --- |
| Ein | TYP I | eth0.tenant | eno1.tenant | Client zu HLI/HSR |
| B | TYP I | eth2.tenant | eno3.tenant | Kommunikation zwischen Knoten |
| C | TYP I | eth1.tenant | eno2.tenant | Knoten zu Speicher |
| D | TYP I | eth4.tenant | eno4.tenant | Konfiguriert, aber nicht verwendet |
| Ein | TYP II | vlan\<tenantNo> | team0.tenant | Client zu HLI/HSR |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Kommunikation zwischen Knoten |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Knoten zu Speicher |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfiguriert, aber nicht verwendet |

### <a name="storage"></a>Storage
Die folgenden Bereitstellungspunkte sind vorkonfiguriert:

| Mountpunkt | Anwendungsfall | 
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
|/hana/logbackups/SID | SID für die Wiederholungsprotokolle für die Produktion |


### <a name="key-considerations"></a>Wichtige Aspekte
- /usr/sap/SID ist eine symbolische Verknüpfung mit /hana/shared/SID.
- Am DR-Standort: Die Volumes und Bereitstellungspunkte sind für die Installation der HANA-Produktionsinstanz auf der HLI-Einheit für die Notfallwiederherstellung konfiguriert. 
- Der primäre Standortknoten führt über die HANA-Systemreplikation eine Synchronisierung mit dem DR-Knoten durch. 
- Mit [Global Reach](../../../expressroute/expressroute-global-reach.md) werden die ExpressRoute-Leitungen miteinander verbunden, um ein privates Netzwerk zwischen Ihren regionalen Netzwerken aufzubauen.


## <a name="next-steps"></a>Nächste Schritte
- [Infrastruktur und Konnektivität](./hana-overview-infrastructure-connectivity.md) für große HANA-Instanzen
- [Hochverfügbarkeit und Notfallwiederherstellung](./hana-overview-high-availability-disaster-recovery.md) für große HANA-Instanzen
