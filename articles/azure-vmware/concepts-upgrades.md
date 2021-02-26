---
title: 'Konzepte: Updates und Upgrades für private Clouds'
description: Hier erfahren Sie mehr über die wichtigsten Upgradeprozesse und Features in Azure VMware Solution.
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 78d4b566aa9156cdddfdcd69b50ebfd1d10aa784
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100006700"
---
# <a name="azure-vmware-solution-private-cloud-updates-and-upgrades"></a>Azure VMware Solution: Updates und Upgrades für private Clouds

Einer der Hauptvorteile von privaten Azure VMware Solution-Clouds besteht darin, dass die Plattform für Sie verwaltet wird. Die Plattformwartung umfasst automatische Updates eines von VMware überprüften Softwarepakets, wodurch sichergestellt wird, dass Sie mithilfe der neuesten Version der überprüften Software für private Clouds in Azure VMware Solution arbeiten.

Insbesondere umfasst eine private Cloud in Azure VMware Solution Folgendes:

- Dedizierte Bare-Metal-Serverknoten, die mit VMware ESXi-Hypervisor bereitgestellt werden 
- vCenter-Server für die Verwaltung von ESXi und vSAN 
- Softwaredefinierte VMware NSX-T-Netzwerke für virtuelle Computer für vSphere-Workloads  
- VMware vSAN-Datenspeicher für virtuelle Computer für vSphere-Workloads  
- VMware HCX für Workloadmobilität  

Zusätzlich zu diesen Komponenten umfasst eine private Cloud von Azure VMware Solution die in Azure zugrunde liegenden Ressourcen, die für die Konnektivität und den Betrieb der privaten Cloud erforderlich sind. Azure VMware Solution überwacht kontinuierlich sowohl die Integrität der zugrunde liegenden Ressourcen als auch der Integrität der VMware-Komponenten. Wenn Azure VMware Solution einen Fehler erkennt, ergreift es Maßnahmen, um die fehlerhaften Komponenten zu reparieren. 

## <a name="what-components-get-updated"></a>Welche Komponenten werden aktualisiert?   

Azure VMware Solution aktualisiert die folgenden VMware-Komponenten: 

- vCenter-Server und ESXi, die auf den Bare-Metal-Serverknoten ausgeführt werden 
- vSAN 
- NSX-T 

Azure VMware Solution aktualisiert auch die Software in den zugrunde liegenden Ressourcen, z. B. Treiber, Software auf den Netzwerkswitches und Firmware auf den Bare-Metal-Knoten. 

## <a name="types-of-updates"></a>Updatetypen

Azure VMware Solution wendet die folgenden Arten von Updates auf VMware-Komponenten an:

- Patches: Sicherheitspatches und Fehlerbehebungen, die von VMware freigegeben werden. 
- Updates: Geringfügige Versionsupdates für eine oder mehrere VMware-Komponenten. 
- Upgrades: Größere Versionsupdates für eine oder mehrere VMware-Komponenten.

Sie werden benachrichtigt, bevor und nachdem Patches auf Ihre privaten Clouds angewendet werden. Wir arbeiten auch mit Ihnen zusammen, um ein Wartungsfenster zu planen, bevor wir Updates oder Upgrades auf Ihre private Cloud anwenden. 

## <a name="vmware-appliance-backup"></a>Sicherung einer VMware-Appliance 

Zusätzlich zur Anwendung von Updates nimmt Azure VMware Solution eine Sicherung der Konfiguration der folgenden VMware-Komponenten vor:

- vCenter Server 
- NSX-T Manager 

Wenn Fehler auftreten kann Azure VMware Solution diese aus der Sicherung der Konfiguration wiederherstellen. 

Weitere Informationen zu VMware-Softwareversionen finden Sie im [Konzeptartikel zu privaten Clouds und Clustern](concepts-private-clouds-clusters.md) und in den [häufig gestellten Fragen (FAQ)](faq.yml).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit den Prozessen und Funktionen zum Schlüsselupgrade in Azure VMware Solution vertraut gemacht haben, informieren Sie sich über die folgenden Themen:

- [Erstellen einer privaten Cloud](tutorial-create-private-cloud.md)
- [Aktivieren einer Azure VMware Solution-Ressource](enable-azure-vmware-solution.md)

<!-- LINKS - external -->

<!-- LINKS - internal -->
