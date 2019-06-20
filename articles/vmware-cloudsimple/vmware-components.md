---
title: 'Azure-VMware-Lösung von CloudSimple: VMware-Komponenten in der privaten Cloud'
description: Beschreibt, wie VMware-Komponenten in einer privaten Cloud installiert werden
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5e6548a5a04e32b374a8a9c29d2ca5f89fd65c78
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65157948"
---
# <a name="private-cloud-vmware-components"></a>VMware-Komponenten in der privaten Cloud

Eine private Cloud ist eine isolierte VMware-Stapelumgebung (ESXi-Hosts, vCenter, vSAN und NSX), die von einem vCenter-Server in einer Verwaltungsdomäne verwaltet wird.  Der CloudSimple-Dienst ermöglicht Ihnen, VMware in einer Hardwareinfrastruktur an Azure-Standorten nativ bereitzustellen.  Private Clouds sind in den Rest der Azure-Cloud integriert.  Eine private Cloud wird mit den folgenden VMware-Stapelkomponenten bereitgestellt:

* **VMware ESXi:** Hypervisor auf dedizierten Azure-Knoten
* **VMware vCenter:** Appliance für die zentrale Verwaltung der vSphere-Umgebung in der privaten Cloud
* **VMware vSAN:** Lösung für hyperkonvergente Infrastruktur
* **VMware NSX Data Center:** Netzwerkvirtualisierungs- und Sicherheitssoftware  

## <a name="vmware-component-versions"></a>Versionen der VMware-Komponenten

Ein VMware-Stapel in einer privaten Cloud wird mit den folgenden Softwareversionen bereitgestellt.

| Komponente | Version | Lizenzierte Version |
|-----------|---------|------------------|
| ESXi | 6.7U1 | Enterprise Plus |
| vCenter | 6.7U1 | vCenter Standard |
| vSAN | 6.7 | Enterprise |
| NSX Data Center | 2.3 | Erweitert |

## <a name="esxi"></a>ESXi

VMware ESXi wird auf erworbenen CloudSimple-Knoten installiert, wenn Sie eine private Cloud erstellen.  ESXi bietet den Hypervisor für die Bereitstellung von Workload-VMs.  Die Knoten bieten eine hyperkonvergente Infrastruktur (Compute und Speicher) in Ihrer privaten Cloud.  Die Knoten sind ein Teil des vSphere-Clusters in der privaten Cloud.  Jeder Knoten verfügt über vier physische Netzwerkschnittstellen, die mit dem zugrunde liegenden Netzwerk verbunden sind.  Mit zwei physischen Netzwerkschnittstellen wird ein **vSphere Distributed Switch (VDS)** in vCenter erstellt, und mit zwei Netzwerkschnittstellen wird ein **über NSX verwalteter virtueller verteilter Switch (N-VDS)** erstellt.  Die Netzwerkschnittstellen werden im Aktiv/Aktiv-Modus für Hochverfügbarkeit konfiguriert.

Weitere Informationen zu VMware ESXi

## <a name="vcenter-server-appliance"></a>vCenter-Serverappliance

Die vCenter-Serverappliance (VCSA) stellt die Funktionen für Authentifizierung, Verwaltung und Orchestrierung für die VMware-Lösung von CloudSimple bereit. Eine VCSA mit eingebettetem Platform Services Controller (PSC) wird bereitgestellt, wenn Sie Ihre private Cloud erstellen.  Die VCSA wird im vSphere-Cluster bereitgestellt, der beim Bereitstellen der privaten Cloud erstellt wird.  Jede private Cloud weist eine eigene VCSA auf.  Die Erweiterung einer privaten Cloud fügt der VCSA in der privaten Cloud die Knoten hinzu.

### <a name="vcenter-single-sign-on"></a>Einmaliges Anmelden für vCenter

Dem eingebetteten Platform Services Controller auf der VCSA ist eine **vCenter-SSO-Domäne** (Single Sign-On, einmaliges Anmelden) zugeordnet.  Der Domänenname lautet **cloudsimple.local**.  Ein Standardbenutzer **CloudOwner@cloudsimple.com** wird für den Zugriff auf vCenter erstellt.  Sie können Ihre lokalen oder in Azure Active Directory verwalteten [Identitätsquellen für vCenter](https://docs.azure.cloudsimple.com/set-vcenter-identity/) hinzufügen.

## <a name="vsan-storage"></a>vSAN-Speicher

Private Clouds werden mit vollständig konfiguriertem vSAN-Nur-Flash-Speicher lokal im Cluster erstellt.  Es sind mindestens drei Knoten der gleichen SKU erforderlich, um einen vSphere-Cluster mit vSAN-Datenspeicher zu erstellen.  Im vSAN-Datenspeicher sind Deduplizierung und Komprimierung standardmäßig aktiviert.  Auf jedem Knoten des vSphere-Clusters werden zwei Datenträgergruppen erstellt. Jede Datenträgergruppe enthält einen Cachedatenträger und drei Kapazitätsdatenträger.

Eine Standard-vSAN-Speicherrichtlinie wird im vSphere-Cluster erstellt und auf den vSAN-Datenspeicher angewandt.  Diese Richtlinie legt fest, wie die VM-Speicherobjekte im Datenspeicher bereitgestellt und zugeordnet werden, um die erforderliche Dienstebene zu gewährleisten.  Die Speicherrichtlinie definiert die **zu tolerierenden Fehler** (Failures To Tolerate, FTT) und die **Fehlertoleranzmethode**.  Sie können neue Speicherrichtlinien erstellen und auf die VMs anwenden. Um die SLA zu gewährleisten, muss im vSAN-Datenspeicher eine freie Kapazität von 25 % aufrechterhalten werden.  

### <a name="default-vsan-storage-policy"></a>vSAN-Standardspeicherrichtlinie

Die nachstehende Tabelle zeigt die vSAN-Standardspeicherrichtlinien-Parameter.

| Anzahl von Knoten im vSphere-Cluster | FTT | Fehlertoleranzmethode |
|------------------------------------|-----|--------------------------|
| 3 und 4 Knoten | 1 | RAID 1 (Spiegelung) – erstellt 2 Kopien |
| 5 bis 16 Knoten | 2 | RAID 1 (Spiegelung) – erstellt 3 Kopien |

## <a name="nsx-data-center"></a>NSX Data Center

NSX Data Center bietet Netzwerkvirtualisierung, Mikrosegmentierung und Netzwerksicherheitsfunktionen in Ihrer privaten Cloud.  Sie können alle von NSX Data Center unterstützten Dienste in Ihrer privaten Cloud über NSX konfigurieren.  Wenn Sie eine private Cloud erstellen, werden die folgenden NSX-Komponenten installiert und konfiguriert.

* NSXT Manager
* Transportzonen
* Host- und Edgeuplinkprofil
* Logischer Switch für Edgetransport, Ext1 und Ext2
* IP-Pool für ESXi-Transportknoten
* IP-Pool für Edgetransportknoten
* Edgeknoten
* DRS-Antiaffinitätsregel für Controller und Edge-VMs
* Router der Ebene 0
* Aktivieren von BGP auf Router der Ebene 0

## <a name="vsphere-cluster"></a>vSphere-Cluster

ESXi-Hosts werden als Cluster konfiguriert, um Hochverfügbarkeit der privaten Cloud sicherzustellen.  Wenn Sie eine private Cloud erstellen, werden im ersten Cluster Verwaltungskomponenten von vSphere bereitgestellt.  Ein Ressourcenpool für Verwaltungskomponenten wird erstellt, und alle Verwaltungs-VMs werden in diesem Ressourcenpool bereitgestellt. Der erste Cluster kann nicht gelöscht werden, um die private Cloud zu verkleinern.  Ein vSphere-Cluster bietet Hochverfügbarkeit für virtuelle Computer über **vSphere HA**.  Zu tolerierende Fehler basieren auf der Anzahl der verfügbaren Knoten im Cluster.  Sie können die Formel ```Number of nodes = 2N+1``` verwenden, wobei ```N``` die Anzahl der zu tolerierenden Fehler ist.

### <a name="vsphere-cluster-limits"></a>vSphere-Clusterlimits

| Resource | Begrenzung |
|----------|-------|
| Mindestanzahl von Knoten zum Erstellen einer privaten Cloud (erster vSphere-Cluster) | 3 |
| Maximale Anzahl von Knoten in einem vSphere-Cluster in einer privaten Cloud | 16 |
| Maximale Anzahl von Knoten in einer privaten Cloud | 64 |
| Maximale Anzahl von vSphere-Clustern in einer privaten Cloud | 21 |
| Mindestanzahl von Knoten in einem neuem vSphere-Cluster | 3 |

## <a name="vmware-infrastructure-maintenance"></a>VMware-Infrastrukturverwaltung

Gelegentlich ist es notwendig, Änderungen an der Konfiguration der VMware-Infrastruktur vorzunehmen. Derzeit können diese Intervalle alle 1-2 Monate auftreten, aber es wird erwartet, dass die Häufigkeit mit der Zeit abnimmt. Diese Art der Wartung kann in der Regel ohne Unterbrechung der normalen Nutzung der CloudSimple-Dienste durchgeführt werden. Während eines VMware-Wartungsintervalls funktionieren die folgenden Dienste weiterhin ohne Beeinträchtigung:

* VMware-Verwaltungsebene und Anwendungen
* vCenter-Zugriff
* Alle Netzwerk und Speicher
* Der gesamte Azure-Datenverkehr

## <a name="updates-and-upgrades"></a>Updates und Upgrades

CloudSimple ist verantwortlich für die Lebenszyklusverwaltung der VMware-Software (ESXi, vCenter, PSC und NSX) in der privaten Cloud.

Softwareupdates umfassen:

* **Patches**. Sicherheitspatches oder Fehlerbehebungen, die von VMware freigegeben werden.
* **Updates**. Ändern der Nebenversion einer VMware-Stack-Komponente.
* **Upgrades**. Ändern der Hauptversion einer VMware-Stack-Komponente.

CloudSimple testet einen wichtigen Sicherheitspatch, sobald er von VMware verfügbar ist. Per SLA rollt CloudSimple den Sicherheitspatch innerhalb einer Woche in private Cloud-Umgebungen aus.

CloudSimple bietet vierteljährliche Wartungsupdates für VMware-Softwarekomponenten. Wenn eine neue Hauptversion der VMware-Software verfügbar ist, arbeitet CloudSimple mit Kunden zusammen, um ein geeignetes Wartungsfenster für das Upgrade zu koordinieren.  

## <a name="next-steps"></a>Nächste Schritte

* [CloudSimple-Wartung und -Updates](cloudsimple-maintenance-updates.md)