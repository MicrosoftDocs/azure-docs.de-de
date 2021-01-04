---
title: SAP Business One in Azure Virtual Machines | Microsoft-Dokumentation
description: SAP Business One in Azure.
author: msjuergent
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 07/15/2018
ms.author: juergent
ms.reviewer: cynthn
ms.openlocfilehash: 1201ff91634a1f63c8fe0e545ca6d6dee33abe35
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957383"
---
# <a name="sap-business-one-on-azure-virtual-machines"></a>SAP Business One in Azure Virtual Machines
Dieses Dokument enthält Anleitungen zum Bereitstellen von SAP Business One in Azure Virtual Machines. Diese Dokumentation ist kein Ersatz für die Installationsdokumentation für Business One für SAP. Die Dokumentation enthält grundlegende Planungs- und Bereitstellungsrichtlinien für die Azure-Infrastruktur zur Ausführung von Business One-Anwendungen.

Business One unterstützt zwei verschiedene Datenbanken:
- SQLServer - weitere Informationen finden Sie unter [SAP Note #928839 - Release Planning for Microsoft SQL Server](https://launchpad.support.sap.com/#/notes/928839) (SAP-Hinweis #928839 – Freigabeplanung für Microsoft SQL Server)
- SAP HANA – die genaue SAP Business One-Supportmatrix für SAP HANA finden Sie in der [SAP-Produktverfügbarkeitsmatrix](https://support.sap.com/pam)

In Bezug auf SQL Server gelten die grundlegenden Bereitstellungsüberlegungen, wie sie in der Dokumentation [Azure Virtual Machines – DBMS-Bereitstellung für SAP NetWeaver](./dbms_guide_general.md) beschrieben sind. Die Überlegungen zu SAP HANA sind in diesem Dokument beschrieben.

## <a name="prerequisites"></a>Voraussetzungen
Um dieses Handbuch zu verwenden, benötigen Sie grundlegende Kenntnisse der folgenden Azure-Komponenten:

- [Azure Virtual Machines auf Windows](../../windows/tutorial-manage-vm.md)
- [Azure Virtual Machines auf Linux](../../linux/tutorial-manage-vm.md)
- [Verwaltung von Azure-Netzwerken und virtuellen Netzwerken mit PowerShell](../../windows/tutorial-virtual-network.md)
- [Azure-Netzwerk und virtuelle Netzwerke mit CLI](../../linux/tutorial-virtual-network.md)
- [Verwalten von Azure-Datenträgern mit der Azure-Befehlszeilenschnittstelle](../../linux/tutorial-manage-disks.md)

Wenn Sie sich nur für Business One interessieren, finden Sie im Dokument [Azure Virtual Machines – Planung und Implementierung für SAP NetWeaver](./planning-guide.md) hilfreiche Informationen.

Es wird davon ausgegangen, dass Sie als die Instanz, die SAP Business One bereitstellt, mit folgenden Aspekten vertraut sind:

- Installation von SAP HANA auf einer vorhandenen Infrastruktur, wie eine VM
- Installation der SAP Business One-Anwendung auf einer Infrastruktur, wie Azure-VMs
- Betrieb von SAP Business One und des ausgewählten DBMS-Systems
- Bereitstellung von Infrastruktur in Azure

Alle diese Bereiche werden in diesem Dokument nicht behandelt.

Neben der Azure-Dokumentation sollten Sie die wichtigsten SAP-Hinweise beachten, die sich auf Business One beziehen oder zentrale Hinweise von SAP für Business One sind:

- [528296 - General Overview Note for SAP Business One Releases and Related Products](https://launchpad.support.sap.com/#/notes/528296) (528296 – Allgemeine Übersicht – Hinweis für SAP Business One-Freigaben und dazugehörige Produkte
- [2216195 - Release Updates Note for SAP Business One 9.2, version for SAP HANA](https://launchpad.support.sap.com/#/notes/2216195) (Freigabeaktualisierung – Hinweis für SAP Business One 9.2, Version für SAP HANA)
- [2483583 - Central Note for SAP Business One 9.3](https://launchpad.support.sap.com/#/notes/2483583) (2483583– Zentraler Hinweis für SAP Business One 9.3)
- [2483615 - Release Updates Note for SAP Business One 9.3](https://launchpad.support.sap.com/#/notes/2483615) (Freigabeaktualisierung – Hinweis für SAP Business One 9.3)
- [2483595 - Collective Note for SAP Business One 9.3 General Issues](https://launchpad.support.sap.com/#/notes/2483595) (Kollektiver Hinweis für SAP Business One 9.3 – Allgemeine Probleme)
- [2027458 - Collective Consulting Note for SAP HANA-Related Topics of SAP Business One, version for SAP HANA](https://launchpad.support.sap.com/#/notes/2027458) (Kollektiver Consulting-Hinweis für SAP HANA-bezogene Themen von SAP Business One, Version für SAP HANA)


## <a name="business-one-architecture"></a>Business One-Architektur
Business One ist eine Anwendung mit zwei Ebenen:

- Eine Clientebene mit einem Fat Client
- Eine Datenbankebene, die das Datenbankschema für einen Mandanten enthält

Eine bessere Übersicht darüber, welche Komponenten im Clientteil und welche Teile im Serverteil ausgeführt werden, finden Sie in [SAP Business One Administrator's Guide](https://help.sap.com/doc/601fbd9113be4240b81d74626439cfa9/10.0/en-US/AdministratorGuide_SQL.pdf) (Administratorhandbuch für SAP Business One). 

Da es eine starke latenzkritische Interaktion zwischen der Clientebene und der DBMS-Ebene gibt, müssen sich beide Ebenen in Azure befinden, wenn sie in Azure bereitgestellt werden. Es ist üblich, dass die Benutzer dann RDS in eine oder mehrere VMs mit einem RDS-Dienst für die Business One-Clientkomponenten einfügen.

### <a name="sizing-vms-for-sap-business-one"></a>Dimensionieren von VMs für SAP Business One

Die Ressourcenanforderungen zur Dimensionierung der Client-VM(s) finden Sie im SAP-Dokument [SAP Business One Hardware Requirements Guide](https://help.sap.com/doc/bfa9770d12284cce8509956dcd4c5fcb/9.3/en-US/B1_Hardware_Requirements_Guide.pdf) (Handbuch zur Hardwareanforderungen für SAP Business One). Für Azure müssen Sie sich auf die in Kapitel 2.4 des Dokuments genannten Anforderungen konzentrieren und diese zur Berechnung verwenden.

Zum Hosten der Business One-Clientkomponenten des DBMS-Hosts sind nur Azure-VMs zugelassen, die von SAP NetWeaver unterstützt werden. Eine Liste der von SAP NetWeaver unterstützten Azure-VMs finden Sie in [SAP Note #1928533](https://launchpad.support.sap.com/#/notes/1928533) (SAP-Hinweis #1928533).

Wird SAP HANA als DBMS-Back-End für Business One ausgeführt, werden für HANA nur VMs unterstützt, die für Business One in der [HANA certified IaaS platform list](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) (Liste der HANA-zertifizierten Plattformen) aufgeführt sind. Die Business One-Clientkomponenten sind von dieser stärkere Einschränkung für SAP HANA als DBMS-System nicht betroffen.

### <a name="operating-system-releases-to-use-for-sap-business-one"></a>Betriebssystemversionen für die Verwendung von SAP Business One

Prinzipiell ist es immer am besten, die aktuellsten Betriebssystemversionen zu verwenden. Vor allem im Linux-Bereich wurde die neue Azure-Funktionalität mit verschiedenen neueren Minor-Versionen von Suse und Red Hat eingeführt. Bezüglich Windows wird die Verwendung von Windows Server 2016 dringend empfohlen.


## <a name="deploying-infrastructure-in-azure-for-sap-business-one"></a>Bereitstellen von Infrastruktur in Azure für SAP Business One
In den nächsten Kapiteln finden Sie die Infrastrukturkomponenten, die für die Bereitstellung von SAP wichtig sind.

### <a name="azure-network-infrastructure"></a>Azure-Netzwerkinfrastruktur
Die Netzwerkinfrastruktur, die Sie in Azure bereitstellen müssen, hängt davon ab, ob Sie ein einziges Business One-System für sich selbst bereitstellen, oder ob Sie ein Hoster sind, der Dutzende von Business One-Systemen für Kunden hostet. Es kann auch geringfügige Änderungen im Design geben, je nachdem, wie Sie sich mit Azure verbinden. Es gibt verschiedene Möglichkeiten: bei einem Design erfolgt eine VPN-Verbindung zu Azure und Sie können Ihr Active Directory über [VPN](../../../vpn-gateway/vpn-gateway-about-vpngateways.md) oder [ExpressRoute](../../../expressroute/expressroute-introduction.md) zu Azure erweitern.

![Einfache Netzwerkkonfiguration mit Business One](./media/business-one-azure/simple-network-with-VPN.PNG)

Die vorgestellte vereinfachte Konfiguration führt mehrere Sicherheitsinstanzen ein, mit denen Sie das Routing steuern und einschränken können. Sie beginnt mit 

- Dem Router bzw. der Firewall lokal auf Kundenseite.
- Die nächste Instanz ist die [Azure-Netzwerksicherheitsgruppe](../../../virtual-network/network-security-groups-overview.md), mit der Sie Routing und Sicherheitsregeln für das Azure VNet einführen können, in dem Sie Ihre SAP Business One-Konfiguration ausführen.
- Um zu vermeiden, dass Benutzer von Business One-Clients auch den Server sehen können, auf dem der Business One-Server läuft, der die Datenbank ausführt, sollten Sie die VM, die den Business One-Client hostet, und den Business One-Server in zwei verschiedene Subnetze innerhalb des VNet trennen.
- Um den Zugriff auf den Business One Server einzuschränken, verwenden Sie erneut die Azure-Netzwerksicherheitsgruppe, die den zwei verschiedenen Subnetzen zugewiesen ist.

Eine komplexere Version einer Azure-Netzwerkkonfiguration basiert auf den für Azure [dokumentierten Best Practices der Hub-and-Spoke-Architektur](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). Das Architekturmuster für Hub und Spoke würden die erste vereinfachte Konfiguration wie folgt ändern:


![Hub-Spoke-Netzwerkkonfiguration mit Business One](./media/business-one-azure/hub-spoke-network-with-VPN.PNG)

Für den Fall, dass sich die Benutzer über das Internet ohne private Konnektivität mit Azure verbinden, muss das Design des Netzwerks in Azure mit den in der Azure-Referenzarchitektur für [DMZ zwischen Azure und dem Internet](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz) dokumentierten Prinzipien übereinstimmen.

### <a name="business-one-database-server"></a>Business One-Datenbankserver
SQL Server und SAP HANA sind als Datenbanktyp verfügbar. Unabhängig vom DBMS sollten Sie das Dokument [Überlegungen zur Azure Virtual Machines-DBMS-Bereitstellung für SAP-Workload](./dbms_guide_general.md) lesen, um ein allgemeines Verständnis der DBMS-Implementierungen in Azure-VMs und der damit verbundenen Netzwerk- und Speicherthemen zu erhalten.

Obwohl dies bereits in den spezifischen und allgemeinen Datenbankdokumenten hervorgehoben, sollten Sie sich mit folgenden Themen vertraut machen:

- [Verwalten der Verfügbarkeit virtueller Windows-Computer in Azure](../../manage-availability.md) und [Verwalten der Verfügbarkeit virtueller Linux-Computer](../../manage-availability.md)
- [SLA für Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)

Diese Dokumente sollen Ihnen bei der Auswahl der Speichertypen und der Hochverfügbarkeitskonfiguration unterstützen.

Im Prinzip sollten Sie folgende Punkte berücksichtigen:

- Verwenden Sie Premium SSDs statt Standard-HDDs. Weitere Informationen zu den verfügbaren Datenträgertypen finden Sie in unserem Artikel [Auswählen eines Datenträgertyps](../../disks-types.md).
- Verwenden Sie Azure Managed Disks anstelle von nicht verwalteten Datenträgern.
- Stellen Sie sicher, dass Sie einen ausreichenden IOPS- und E/A-Durchsatz zusammen mit Ihrer Datenträgerkonfiguration konfiguriert haben.
- Kombinieren Sie /hana/data- und /hana/log-Volumes, um eine kosteneffiziente Speicherkonfiguration zu erhalten.


#### <a name="sql-server-as-dbms"></a>SQL Server als DBMS
Informationen zum Bereitstellen von SQL Server als DBMS für Business One finden Sie im Dokument [Azure Virtual Machines – DBMS-Bereitstellung für SAP NetWeaver](./dbms_guide_sqlserver.md). 

Grobe Größenschätzungen für die DBMS-Seite für SQL Server sind:

| Anzahl an Benutzern | vCPUs | Arbeitsspeicher | Beispiele für VM-Typen |
| --- | --- | --- | --- |
| bis zu 20 | 4 | 16 GB | D4s_v3, E4s_v3 |
| bis zu 40 | 8 | 32 GB | D8s_v3, E8s_v3 |
| bis zu 80 | 16 | 64 GB | D16s_v3, E16s_v3 |
| bis zu 150 | 32 | 128 GB | D32s_v3, E32s_v3 |

Die oben angegebenen Größen geben Ihnen einen Anhaltspunkt, wie Sie beginnen können. Möglicherweise benötigen Sie weniger oder mehr Ressourcen. Dies können Sie in Azure ganz einfach anpassen. Um den VM-Typen zu wechseln, muss nur die VM neu gestartet werden.

#### <a name="sap-hana-as-dbms"></a>SAP HANA als DBMS
Wenn Sie SAP HANA als DBMS verwenden, sollten Sie die Informationen im Dokument [SAP HANA in Azure-Vorgängen – Anleitung](./hana-vm-operations.md) in den folgenden Abschnitten berücksichtigen.

Für Hochverfügbarkeits- und Notfallwiederherstellungs-Konfigurationen rund um SAP HANA als Datenbank für Business One in Azure sollten Sie die Dokumentation [Hochverfügbarkeit von SAP HANA für virtuelle Azure-Computer](./sap-hana-availability-overview.md) und die darin aufgeführten Dokumente lesen.

Für SAP HANA Sicherungs- und Wiederherstellungsstrategien sollten Sie das Dokument [Sicherungsanleitung für SAP HANA in Azure Virtual Machines](./sap-hana-backup-guide.md) und die darin aufgeführten Dokumente lesen.

 
### <a name="business-one-client-server"></a>Business One-Clientserver
Für diese Komponenten sind Überlegungen zur Speicherung nicht das Hauptanliegen. Trotzdem möchten Sie eine zuverlässige Plattform zusammenstellen. Aus diesem Grund sollten Sie auch für die Basis-VHD Azure Storage Premium für diesen virtuellen Computer verwenden. Dimensionieren Sie die VM mit den in [SAP Business One Hardware Requirements Guide](https://help.sap.com/doc/bfa9770d12284cce8509956dcd4c5fcb/9.3/en-US/B1_Hardware_Requirements_Guide.pdf) (Handbuch zur Hardwareanforderungen für SAP Business One) angegebenen Daten. Für Azure müssen Sie sich auf die in Kapitel 2.4 des Dokuments genannten Anforderungen konzentrieren und diese zur Berechnung verwenden. Bei der Berechnung der Anforderungen müssen Sie diese mit den folgenden Dokumenten vergleichen, um die für Sie ideale VM zu finden:

- [Größen für virtuelle Windows-Computer in Azure](../../sizes.md)
- [SAP Note #1928533](https://launchpad.support.sap.com/#/notes/1928533) (SAP-Hinweis #1928533)

Vergleichen Sie die erforderlich Anzahl an CPUs und den benötigten Speicher mit den Angaben von Microsoft. Berücksichtigen Sie bei der Auswahl der VMs auch den Netzwerkdurchsatz.
