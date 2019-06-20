---
title: VLANs und Subnetze in VMware-Lösung von CloudSimple – Azure
description: Erfahren Sie mehr über VLANs und Subnetze in einer privaten CloudSimple-Cloud.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9d29445054848d798476fed8184b89f9b6c1210f
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497578"
---
# <a name="vlans-and-subnets-overview"></a>Übersicht über VLANs und Subnetze

CloudSimple stellt ein Netzwerk pro Region bereit, in der Ihr CloudSimple-Dienst bereitgestellt wird.  Das Netzwerk ist ein einzelner TCP-Schicht-3-Adressraum mit standardmäßig aktiviertem Routing.  Alle privaten Clouds und Subnetze, die in dieser Region erstellt wurden, können ohne zusätzliche Konfiguration miteinander kommunizieren.  Sie können mit den VLANs verteilte Portgruppen im vCenter erstellen.

![CloudSimple-Netzwerktopologie](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>VLANs

VLANs (Schicht-2-Netzwerk) werden pro privater Cloud erstellt.  Der Schicht-2-Datenverkehr bleibt innerhalb der Grenzen einer privaten Cloud, sodass Sie den lokalen Datenverkehr in der privaten Cloud isolieren können.  Ein VLAN, das in einer privaten Cloud erstellt wurde, kann nur dazu verwendet werden, verteilte Portgruppen in dieser privaten Cloud zu erstellen.  Ein VLAN, das in einer privaten Cloud erstellt wurde, wird automatisch auf allen Switches konfiguriert, die mit den Hosts der privaten Cloud verbundenen sind.

## <a name="subnets"></a>Subnetze

Sie können beim Erstellen eines VLANs ein Subnetz erstellen, indem Sie den Adressraum des Subnetzes definieren. Eine IP-Adresse aus dem Adressraum wird als ein Subnetzgateway zugewiesen. Ein einzelner privater Schicht-3-Adressraum wird pro Kunde und Region zugewiesen. Sie können jeden nicht überlappenden RFC 1918-Adressraum mit Ihrem lokalen Netzwerk oder virtuellen Azure-Netzwerk in Ihrer Netzwerkregion konfigurieren.

Alle Subnetze können standardmäßig miteinander kommunizieren, wodurch der Konfigurationsmehraufwand zwischen privaten Clouds verringert wird. Ost-West-Daten zwischen PCs in derselben Region bleiben im selben Schicht-3-Netzwerk und werden über die lokale Netzwerkinfrastruktur innerhalb der Region übertragen. Es sind keine ausgehenden Daten für die Kommunikation zwischen privaten Clouds in einer Region erforderlich. Mit diesem Ansatz werden WAN-/Ausgangsleistungseinbußen vermieden, die sich durch Bereitstellen verschiedener Workloads in verschiedenen privaten Clouds ergeben würden.

## <a name="vspherevsan-subnets-cidr-range"></a>CIDR-Bereich für vSphere/vSAN-Subnetze

Eine private Cloud wird als isolierte VMware-Stapelumgebung (ESXi-Hosts, vCenter, vSAN und NSX) erstellt, die von einem vCenter-Server verwaltet wird.  Verwaltungskomponenten werden in dem Netzwerk bereitgestellt, das für **vSphere/vSAN-Subnetze-CIDR** ausgewählt ist.  Der Netzwerk CIDR-Bereich wird während der Bereitstellung in unterschiedliche Subnetze aufgeteilt.

Mindestpräfix für einen vSphere/vSAN-Subnetze-CIDR-Bereich: **/24** Maximales Präfix für einen vSphere/vSAN-Subnetze-CIDR-Bereich: **/21**

> [!CAUTION]
> IP-Adressen im vSphere/vSAN CIDR-Bereich sind zur Verwendung durch die private Cloudinfrastruktur reserviert. Verwenden Sie keine IP-Adresse in diesem Bereich auf einem virtuellen Computer.


### <a name="vspherevsan-subnets-cidr-range-limits"></a>Grenzwerte für vSphere/vSAN-Subnetze-CIDR

Die ausgewählte Bereichsgröße für vSphere/vSAN-Subnetze-CIDR wirkt sich auf die Größe Ihrer privaten Cloud aus.  In der folgenden Tabelle ist für jede Größe von vSphere/vSAN-Subnetze-CIDR die maximal mögliche Anzahl von Knoten aufgeführt.

| Angegebene Länge für vSphere/vSAN-Subnetze-CIDR-Präfix | Maximale Knotenanzahl |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>Verwaltungssubnetze, die in einer privaten Cloud erstellt werden

Die folgenden Verwaltungssubnetze werden erstellt, wenn Sie eine private Cloud erstellen. 

* **Systemverwaltung**: VLAN und Subnetz für das Verwaltungsnetzwerk der ESXi-Hosts, DNS-Server, vCenter-Server
* **vMotion**: VLAN und Subnetz für das vMotion-Netzwerk der ESXi-Hosts
* **vSAN**: VLAN und Subnetz für das vSAN-Netzwerk der ESXi-Hosts
* **NsxtEdgeUplink1**:VLAN und Subnetz für VLAN-Uplinks zu einem externen Netzwerk
* **NsxtEdgeUplink2**:VLAN und Subnetz für VLAN-Uplinks zu einem externen Netzwerk
* **NsxtEdgeTransport**:VLAN und Subnetz für Datentransportzonen steuern die Reichweite von Schicht-2-Netzwerken in NSX-T.
* **NsxtHostTransport**:VLAN und Subnetz für die Host-Datentransportzone

### <a name="management-network-cidr-range-breakdown"></a>Aufschlüsselung eines Verwaltungsnetzwerk-CIDR-Bereichs

Der angegebene vSphere/vSAN-Subnetze-CIDR-Bereich wird in mehrere Subnetze aufgeteilt.  In der folgenden Tabelle ist ein Beispiel für die Aufschlüsselung zulässiger Präfixe aufgeführt.  In dem Beispiel wird **192.168.0.0** als CIDR-Bereich verwendet.

Beispiel:

| Angegebenes vSphere/vSAN-Subnetze-CIDR/Präfix | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| Systemverwaltung | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| NSX-T-Host-Datentransport | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| NSX-T-Edge-Datentransport | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX-T-Edge-Uplink1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| NSX-T-Edge-Uplink2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen und Verwalten von VLANs und Subnetzen](https://docs.azure.cloudsimple.com/create-vlan-subnet/)
