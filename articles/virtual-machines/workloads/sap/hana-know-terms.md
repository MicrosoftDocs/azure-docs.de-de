---
title: Informationen zu Begriffen zu SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Informationen zu Begriffen zu SAP HANA in Azure (große Instanzen)
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7777888bd27325e9094032458922969494a9a64e
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94984956"
---
# <a name="know-the-terms"></a>Informationen zu Begriffen

Im Handbuch zur Architektur und technischen Bereitstellung werden mehrere allgemeine Definitionen häufig verwendet. Beachten Sie die folgenden Begriffe und ihre Bedeutung:

- **IaaS**: Infrastructure-as-a-Service
- **PaaS**: Platform-as-a-Service
- **SaaS**: Software-as-a-Service
- **SAP-Komponente**: Eine einzelne SAP-Anwendung, beispielsweise ERP Central Component (ECC), Business Warehouse (BW), Solution Manager oder Enterprise Portal (EP). SAP-Komponenten können auf herkömmlichen ABAP- oder Java-Technologien oder auf einer Nicht-NetWeaver-basierten Anwendung wie Business Objects basieren.
- **SAP-Umgebung**: Eine oder mehrere SAP-Komponenten, die logisch gruppiert sind, um eine Geschäftsfunktion wie Entwicklung, Qualitätssicherung, Schulung, Notfallwiederherstellung oder Produktion auszuführen.
- **SAP-Landschaft**: Bezieht sich auf alle SAP-Ressourcen in Ihrer IT-Landschaft. Die SAP-Landschaft umfasst alle Produktions- und anderen Umgebungen.
- **SAP-System**: Die Kombination von DBMS-Schicht und Anwendungsschicht, die beispielsweise aus einem SAP ERP-Entwicklungssystem, einem SAP BW-Testsystem und einem SAP CRM-Produktionssystem bestehen kann. Azure-Bereitstellungen unterstützen die Aufteilung dieser beiden Schichten zwischen lokalen Systemen und Azure nicht. Ein SAP-System wird entweder lokal oder in Azure bereitgestellt. Sie können die verschiedenen Systeme einer SAP-Landschaft in Azure oder lokal bereitstellen. Beispielsweise können Sie die SAP CRM-Entwicklungs- und Testsysteme in Azure und das SAP CRM-Produktionssystem lokal bereitstellen. Im Fall von SAP HANA in Azure (große Instanzen) sollen Sie die SAP-Anwendungsschicht von SAP-Systemen auf VMs und die zugehörige SAP HANA-Instanz auf einer Einheit im Umfeld von SAP HANA in Azure (große Instanzen) hosten.
- **Umfeld der großen Instanz**: Ein Hardwareinfrastrukturstapel, der SAP HANA TDI-zertifiziert und für die Ausführung von SAP HANA-Instanzen in Azure zugeordnet ist.
- **SAP HANA in Azure (große Instanzen)** : Offizieller Name für das Angebot in Azure, HANA-Instanzen auf SAP HANA TDI-zertifizierter Hardware auszuführen, die in Umfeldern der großen Instanz in verschiedenen Azure-Regionen bereitgestellt wird. Der Begriff *HANA (große Instanz)* ist die Kurzform für *SAP HANA in Azure (große Instanzen)* und wird in diesem Bereitstellungshandbuch gemeinhin verwendet.
- **Standortübergreifend**: Beschreibt ein Szenario, in dem VMs in einem Azure-Abonnement bereitgestellt werden, das Site-to-Site-, Multisite- oder ExpressRoute-Konnektivität zwischen lokalen Rechenzentren und Azure umfasst. In allgemeinen Azure-Dokumentationen werden diese Arten von Bereitstellungen auch als standortübergreifende Szenarios bezeichnet. Die Verbindung dient dazu, lokale Domänen, lokale Azure Active Directory-/OpenLDAP-Instanzen und den lokalen DNS-Dienst auf Azure zu erweitern. Die lokale Landschaft wird auf die Azure-Ressourcen der Azure-Abonnements erweitert. Durch diese Erweiterung können die VMs Teil der lokalen Domäne sein. 

   Domänenbenutzer der lokalen Domäne können auf die Server zugreifen und Dienste auf den VMs ausführen (beispielsweise DBMS-Dienste). Die Kommunikation und Namensauflösung zwischen lokal bereitgestellten VMs und in Azure bereitgestellten VMs ist möglich. Dieses typische Szenario wird für die Bereitstellung der meisten SAP-Ressourcen genutzt. Weitere Informationen finden Sie unter [Azure VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) und [Erstellen eines virtuellen Netzwerks mit einer Site-to-Site-Verbindung im Azure-Portal](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Tenant:** Ein im Umfeld von HANA (große Instanz) bereitgestellter Kunde wird in einem *Mandanten* isoliert. Ein Mandant wird in der Schicht der Netzwerke, Speicher und von Compute von anderen Mandanten isoliert. Speicher- und Compute-Einheiten, die unterschiedlichen Mandanten zugewiesen sind, können einander auf der Ebene des Umfelds von HANA (große Instanz) nicht sehen und nicht miteinander kommunizieren. Ein Kunde kann Bereitstellungen in unterschiedlichen Mandanten durchführen. Auch dann ist die Kommunikation auf Ebene der großen HANA-Instanz nicht möglich.
- **SKU-Kategorie**: Für HANA (große Instanz) werden die folgenden beiden SKU-Kategorien angeboten:
    - **Typ I-Klasse**: S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 und S224m
    - **Typ II-Klasse**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm und S960m
- **Stamp**: Definiert die interne Microsoft-Bereitstellungsgröße von SAP HANA (große Instanzen). Bevor Einheiten von SAP HANA (große Instanzen) bereitgestellt werden können, muss ein Stamp von SAP HANA (große Instanzen), der aus Compute-, Netzwerk- und Speicherracks besteht, an einem Rechenzentrumsstandort bereitgestellt werden. Eine solche Bereitstellung wird als Stamp für SAP HANA (große Instanzen) bezeichnet. Seit Revision 4 (siehe unten) wird alternativ der Begriff **Reihe für große Instanzen** verwendet.
- **Revision**: Es gibt zwei unterschiedliche Stamprevisionen für Stamps von SAP HANA (große Instanzen). Diese unterscheiden sich in der Architektur und in der Nähe zu Hosts für virtuelle Computer in Azure.
    - „Revision 3“ (Rev 3) ist der ursprüngliche Entwurf, der von Mitte des Jahres 2016 an bereitgestellt wurde.
    - „Revision 4“ (Rev 4) ist ein neuer Entwurf, der größere Nähe zu Hosts für virtuelle Computer in Azure und damit geringere Netzwerklatenz zwischen Azure-VMS und Einheiten von SAP HANA (große Instanzen) bieten kann. 
    - „Revision 4.2“ (Rev 4.2): Für vorhandene DCs der Revision 4 werden Ressourcen in BareMetal-Infrastruktur umbenannt.  Kunden können über das Azure-Portal auf Ihre Ressourcen als BareMetal-Instanzen zugreifen. 

Es sind viele weitere Ressourcen zum Bereitstellen einer SAP-Workload in der Cloud verfügbar. Die Planung einer Bereitstellung von SAP HANA in Azure erfordert, dass Sie mit den Prinzipien von Azure-IaaS und der Bereitstellung von SAP-Workloads in Azure-IaaS vertraut sind. Bevor Sie fortfahren, sollten Sie den Artikel zur [Verwendung von SAP-Lösungen auf Azure-VMs](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lesen. 

**Nächste Schritte**
- Lesen Sie [HLI-Zertifizierung](hana-certification.md).