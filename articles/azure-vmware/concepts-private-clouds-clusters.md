---
title: Konzepte – Private Clouds und Cluster
description: Hier erfahren Sie mehr über die wichtigsten Funktionen von softwaredefinierten Azure VMware Solution-Rechenzentren und vSphere-Clustern.
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: af34bb0d83d9571ae91dce90cab198a61cfe82a1
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967379"
---
#  <a name="azure-vmware-solution-private-cloud-and-cluster-concepts"></a>Azure VMware Solution: Konzepte – Private Clouds und Cluster

Azure VMware Solution bietet VMware-basierte private Clouds in Azure. Private Clouds enthalten Cluster, die mit dedizierten Azure-Bare-Metal-Hosts erstellt werden. Sie werden über das Azure-Portal oder die CLI oder mithilfe von PowerShell bereitgestellt und verwaltet.  Cluster in privaten Clouds werden mit VMware vSphere-, vCenter-, vSAN- und NSX-Software bereitgestellt. Hardware- und Softwarebereitstellungen für private Azure VMware Solution-Clouds sind vollständig in Azure integriert und automatisiert.

Es besteht eine logische Beziehung zwischen Azure-Abonnements, privaten Azure VMware Solution-Clouds, vSAN-Clustern und Hosts. Die Abbildung zeigt ein einzelnes Azure-Abonnement mit zwei privaten Clouds, die für die Entwicklungs- und die Produktionsumgebung stehen.  In jeder dieser privaten Clouds gibt es zwei Cluster. 

Dieser Artikel enthält eine Beschreibung aller dieser Konzepte.

![Abbildung von zwei privaten Clouds in einem Kundenabonnement](./media/hosts-clusters-private-clouds-final.png)

>[!NOTE]
>Aufgrund der geringeren potenziellen Anforderungen einer Entwicklungsumgebung können kleinere Cluster und Hosts mit geringeren Kapazitäten verwendet werden. 

## <a name="private-clouds"></a>Private Clouds

Private Clouds enthalten vSAN-Cluster, die mit dedizierten Azure-Bare-Metal-Hosts erstellt werden. Jede private Cloud kann über mehrere Cluster verfügen, die alle vom selben vCenter Server und NSX-T Manager verwaltet werden. Sie können private Clouds im Portal, über die CLI oder mit PowerShell bereitstellen und verwalten. 

Private Clouds werden wie andere Ressourcen innerhalb eines Azure-Abonnements installiert und verwaltet. Die Anzahl der privaten Clouds innerhalb eines Abonnements ist skalierbar. Anfänglich besteht eine Begrenzung von einer privaten Cloud pro Abonnement.

## <a name="clusters"></a>Cluster
Für jede erstellte private Cloud gibt es standardmäßig ein vSAN-Cluster. Sie können Cluster im Azure-Portal oder über die API hinzufügen, löschen und skalieren.  Alle Cluster verfügen über eine Standardgröße von drei Hosts und können auf bis zu 16 Hosts skaliert werden.  Die in einem Cluster verwendeten Hosts müssen denselben Typ aufweisen.

Testcluster stehen zu Bewertungszwecken zur Verfügung und sind auf drei Hosts begrenzt. Pro privater Cloud gibt es einen einzelnen Testcluster. Sie können einen Testcluster während des Evaluierungszeitraums unter Verwendung eines einzelnen Hosts skalieren.

Sie verwenden vSphere und NSX-T Manager, um die meisten weiteren Aspekte der Clusterkonfiguration oder des Clusterbetriebs zu verwalten. Der gesamte lokale Speicher jedes Hosts in einem Cluster befindet sich unter der Kontrolle von vSAN.

## <a name="hosts"></a>Hosts

In Clustern privater Azure VMware Solution-Clouds werden hyperkonvergente Bare-Metal-Infrastrukturhosts verwendet. Die RAM-, CPU- und Datenträgerkapazitäten des Hosts sind in der folgenden Tabelle angegeben. 

| Hosttyp              |             CPU             |   RAM (GB)   |  vSAN NVMe-Cacheebene (TB, Raw)  |  vSAN SSD-Kapazitätsebene (TB, Raw)  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| High-End (HE)          |  Intel Dual Core, 18 Kerne 2,3 GHz  |     576      |                3.2               |                15,20               |

Hosts, die zum Erstellen oder Skalieren von Clustern verwendet werden, stammen aus einem isolierten Hostpool. Diese Hosts wurden Hardwaretests unterzogen. Darüber hinaus wurden alle Daten sicher gelöscht. 

## <a name="vmware-software-versions"></a>Versionen von VMware-Software

Im Folgenden die aktuellen in Clustern privater Azure VMware Solution-Clouds verwendeten VMware-Softwareversionen:

| Software              |    Version   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6.7 U3    | 
| ESXi                  |    6.7 U3    | 
| vSAN                  |    6.7 U3    |
| NSX-T                 |      2.5     |

Bei allen neuen Clustern in einer privaten Cloud stimmt die Softwareversion mit der aktuell ausgeführten überein. Für alle neuen privaten Clouds in einem Abonnement wird die aktuelle Version des Softwarestapels installiert.

Die allgemeinen Upgraderichtlinien und -prozesse für die Azure VMware Solution-Plattformsoftware werden unter [Updates und Upgrades für private Clouds](concepts-upgrades.md) beschrieben.

## <a name="host-maintenance-and-lifecycle-management"></a>Hostwartung und Lebenszyklusverwaltung

Die Hostwartung und Lebenszyklusverwaltung haben keine Auswirkungen auf die Kapazität oder Leistung von Clustern in privaten Clouds.  Beispiele für die automatisierte Hostwartung sind Firmwareupgrades und Hardwarereparaturen oder der Austausch von Hardware.

Microsoft ist für die Lebenszyklusverwaltung von NSX-T-Appliances wie NSX-T Manager und NSX-T Edge verantwortlich. Microsoft ist auch für das Bootstrapping der Netzwerkkonfiguration zuständig, z. B. die Erstellung des Tier-0-Gateways und die Aktivierung des Nord-Süd-Routings. Sie sind für die NSX-T-DSN-Konfiguration verantwortlich. Dazu zählen beispielsweise Netzwerksegmente, Regeln für verteilte Firewalls, Tier-1-Gateways und Lastenausgleichsmodule.

> [!IMPORTANT]
> Ändern Sie die Konfiguration von NSX-T Edge oder des Tier-0-Gateways nicht, da dies zu einer Beeinträchtigung des Diensts führen kann.

## <a name="backup-and-restoration"></a>Sichern und Wiederherstellen

Die vCenter- und NSX-T-Konfigurationen der privaten Cloud werden stündlich gesichert.  Sicherungen werden drei Tage lang aufbewahrt. Wenn Sie eine Wiederherstellung auf Grundlage einer Sicherung durchführen möchten, öffnen Sie eine [Supportanfrage](https://rc.portal.azure.com/#create/Microsoft.Support) im Azure-Portal, um die Wiederherstellung anzufordern.

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Schritt erfahren Sie mehr über [Netzwerk- und Interkonnektivitätskonzepte](concepts-networking.md).

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

