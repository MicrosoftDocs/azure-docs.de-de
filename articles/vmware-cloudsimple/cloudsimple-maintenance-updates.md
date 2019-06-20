---
title: Azure VMware-Lösung von CloudSimple – CloudSimple-Wartung und -Updates
description: Beschreibt den CloudSimple-Dienstprozess für planmäßige Wartung und Updates.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4dde358f10e9ac5054297ff68a0971404c0dc135
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65157956"
---
# <a name="cloudsimple-maintenance-and-updates"></a>CloudSimple-Wartung und -Updates

Die private Cloudumgebung ist so konzipiert, dass sie keinen Single Point of Failure aufweist:

* ESXi-Cluster werden mit vSphere-Hochverfügbarkeit konfiguriert. Die Cluster sind so bemessen, dass sie mindestens einen Ersatzknoten für die Ausfallsicherheit aufweisen.
* Redundanter Primärspeicher wird von vSAN bereitgestellt. Dafür werden mindestens drei Knoten benötigt, um Schutz vor einem einzigen Ausfall zu bieten. vSAN kann konfiguriert werden, um eine höhere Resilienz für größere Cluster zu gewährleisten.
* vCenter-, PSC- und NSX Manager-VMs sind mit RAID-10-Speicherrichtlinien konfiguriert, um sie vor Speicherausfällen zu schützen. Die VMs sind durch vSphere-Hochverfügbarkeit gegen Knoten-/Netzausfälle geschützt.
* ESXi-Hosts haben redundante Lüfter und NICs.
* Tor- und Spine-Switches sind paarweise für Hochverfügbarkeit konfiguriert, um Resilienz zu gewährleisten.

CloudSimple überwacht kontinuierlich die folgenden VMs auf Betriebszeit und Verfügbarkeit und bietet Verfügbarkeits-SLAs:

* ESXi-Hosts
* vCenter
* PSC
* NSX Manager

CloudSimple überwacht auch die folgenden Komponenten ständig auf Fehler:

* Festplatten
* Physische NIC-Ports
* Server
* Lüfter
* Potenz
* Switches
* Switchports

Wenn ein Datenträger oder Knoten ausfällt, wird automatisch ein neuer Knoten zu dem betroffenen VMware-Cluster hinzugefügt, um ihn sofort wieder in den ordnungsgemäßen Zustand zu versetzen.

CloudSimple sichert, wartet und aktualisiert diese VMware-Elemente in den privaten Clouds:

* ESXi
* VCenter-Plattformdienste
* Controller
* vSAN
* NSX

## <a name="back-up-and-restore"></a>Sichern und Wiederherstellen

Die CloudSimple-Sicherung umfasst:

* Nächtliche inkrementelle Sicherungen von vCenter-, PSC- und DVS-Regeln.
* Verwendung von nativen vCenter-APIs zur Sicherung von Komponenten auf der Anwendungsebene.
* Automatische Sicherung vor allen Updates oder Upgrades der VMware-Verwaltungssoftware
* Datenverschlüsselung an der Quelle, durch vCenter, vor der Datenübertragung über einen TLS1.2-verschlüsselten Kanal zu Azure. Die Daten werden in einem Azure-Blob gespeichert, wo sie über Regionen hinweg repliziert werden.

Sie können eine Wiederherstellung anfordern, indem Sie eine [Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) stellen.

## <a name="maintenance"></a>Wartung

CloudSimple führt mehrere Arten von geplanten Wartungsmaßnahmen durch.

### <a name="backendinternal-maintenance"></a>Wartung von Back-End-/interne Wartung

Diese Wartung beinhaltet in der Regel die Rekonfiguration physischer Objekte oder die Installation von Softwarepatches. Es hat keinen Einfluss auf die normale Nutzung der Objekte. Da redundante NICs zu jedem physischen Rack gehen, ist der normale Netzwerkverkehr und der private Cloud-Betrieb nicht beeinträchtigt. Möglicherweise treten Leistungseinbußen nur dann auf, wenn Ihre Organisation davon ausgeht, die gesamte redundante Bandbreite während des Wartungsintervalls zu nutzen.

### <a name="cloudsimple-portal-maintenance"></a>CloudSimple-Portalwartung

Wenn die Steuerungsebene oder Infrastruktur von CloudSimple aktualisiert wird, sind begrenzte Ausfallzeiten erforderlich. Derzeit können die Wartungsintervalle einmal pro Monat stattfinden. Es wird erwartet, dass die Frequenz mit der Zeit abnimmt. CloudSimple benachrichtigt Sie, wenn eine Portalwartung stattfindet, und hält das Intervall so kurz wie möglich. Während eines Portal-Wartungsintervalls funktionieren die folgenden Dienste weiterhin ohne Beeinträchtigung:

* VMware-Verwaltungsebene und Anwendungen
* vCenter-Zugriff
* Alle Netzwerk und Speicher
* Der gesamte Azure-Datenverkehr

### <a name="vmware-infrastructure-maintenance"></a>VMware-Infrastrukturverwaltung

Gelegentlich ist es notwendig, Änderungen an der Konfiguration der VMware-Infrastruktur vorzunehmen.  Derzeit können diese Intervalle alle 1-2 Monate auftreten, aber es wird erwartet, dass die Häufigkeit mit der Zeit abnimmt. Diese Art der Wartung kann in der Regel ohne Unterbrechung der normalen Nutzung der CloudSimple-Dienste durchgeführt werden. Während eines VMware-Wartungsintervalls funktionieren die folgenden Dienste weiterhin ohne Beeinträchtigung:

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

[Sichern von Workload-VMs mit Veeam](https://docs.azure.cloudsimple.com/backup-workloads-veeam/).