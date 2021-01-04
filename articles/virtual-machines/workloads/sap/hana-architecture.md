---
title: Architektur von SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Architektur zur Bereitstellung von SAP HANA in Azure (große Instanzen)
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a23a177945316bca89aa5cbcc46f840213dbe832
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964812"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Architektur von SAP HANA in Azure (große Instanzen)

Allgemein betrachtet, befindet sich die SAP-Anwendungsschicht bei SAP HANA in Azure (große Instanzen) auf VMs. Die Datenbankschicht befindet sich auf SAP TDI-konfigurierter Hardware in einem Umfeld der großen Instanz in derselben mit Azure-IaaS verbundenen Azure-Region.

> [!NOTE]
> Stellen Sie die SAP-Anwendungsschicht in derselben Azure-Region bereit wie die SAP-DBMS-Schicht. Diese Regel ist in veröffentlichten Informationen zu SAP-Workloads in Azure umfassend dokumentiert. 

Die Gesamtarchitektur von SAP HANA in Azure (große Instanzen) bietet eine SAP TDI-zertifizierte Hardwarekonfiguration, bei der es sich um einen nicht virtualisierten Bare-Metal-Hochleistungsserver für die SAP HANA-Datenbank handelt. Darüber hinaus bietet sie die Möglichkeit und Flexibilität von Azure, Ressourcen für die SAP-Anwendungsschicht nach Bedarf zu skalieren.

![Architekturübersicht über SAP HANA in Azure (große Instanzen)](./media/hana-overview-architecture/image1-architecture.png)

Die dargestellte Architektur ist in drei Abschnitte unterteilt:

- **Rechts:** Zeigt eine lokale Infrastruktur, die verschiedene Anwendungen in Rechenzentren ausführt, sodass Endbenutzer auf branchenspezifische Anwendungen wie SAP zugreifen können. Im Idealfall wird diese lokale Infrastruktur über [ExpressRoute](https://azure.microsoft.com/services/expressroute/) mit Azure verbunden.

- **Mitte:** Zeigt Azure IaaS und (in diesem Fall) die Verwendung von virtuellen Computern zum Hosten von SAP-basierten oder anderen Anwendungen, die SAP HANA als DBMS-System nutzen. Kleinere HANA-Instanzen, die mit dem von VMs bereitgestellten Arbeitsspeicher arbeiten, werden zusammen mit ihrer Anwendungsschicht auf VMs bereitgestellt. Weitere Informationen zu VMs finden Sie unter [Virtuelle Computer](https://azure.microsoft.com/services/virtual-machines/).

   Azure-Netzwerkdienste werden verwendet, um SAP-Systeme mit anderen Anwendungen in virtuellen Netzwerken zu gruppieren. Diese virtuellen Netzwerke stellen sowohl eine Verbindung mit lokalen Systemen als auch mit SAP HANA in Azure (große Instanzen) her.

   Informationen zu SAP NetWeaver-Anwendungen und Datenbanken, deren Ausführung in Azure unterstützt wird, finden Sie im [SAP-Supporthinweis 1928533 – SAP applications on Azure: Supported products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533) (SAP-Anwendungen unter Azure: Unterstützte Produkte und Azure-VM-Typen). Eine Dokumentation zur Bereitstellung von SAP-Lösungen in Azure finden Sie in den Artikeln zu folgenden Themen:

  -  [Verwenden von SAP auf Windows-VMs](./get-started.md?toc=/azure/virtual-machines/linux/toc.json)
  -  [Verwenden von SAP-Lösungen auf Azure-VMs](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Links:** Zeigt die SAP HANA TDI-zertifizierte Hardware im Azure-Umfeld der großen Instanz. Die Einheiten von HANA (große Instanz) sind mit den virtuellen Netzwerken Ihres Azure-Abonnements verbunden. Für diese Verbindung wird die gleiche Technologie verwendet wie für die Konnektivität zwischen lokalen Systemen und Azure. Im Mai 2019 wurde eine Optimierung eingeführt, die die Kommunikation zwischen den Einheiten von HANA (große Instanz) und den virtuellen Azure-Computern ohne Beteiligung des ExpressRoute-Gateways ermöglicht. Diese Optimierung wird als ExpressRoute Fast Path bezeichnet und ist in dieser Architektur dargestellt (rote Linien). 

Im Azure-Stapel für große Instanzen selbst werden die folgenden Komponenten kombiniert:

- **Computing:** Server, die auf verschiedenen Generationen von Intel Xeon-Prozessoren basieren, welche die erforderliche Rechenleistung bereitstellen und SAP HANA-zertifiziert sind.
- **Netzwerk:** Ein einheitliches Hochgeschwindigkeitsnetzwerk-Fabric, das die Computing-, Speicher- und LAN-Komponenten miteinander verbindet.
- **Storage**: Eine Speicherinfrastruktur, auf die über ein einheitliches Netzwerkfabric zugegriffen wird. Die verfügbare spezifische Speicherkapazität hängt von der jeweiligen Konfiguration von SAP HANA in Azure (große Instanzen) ab, die bereitgestellt wird. Mehr Speicherkapazität ist gegen eine zusätzliche monatliche Gebühr erhältlich.

Innerhalb der mehrinstanzenfähigen Infrastruktur des Stapels für große Instanzen werden Kunden als isolierte Mandanten bereitgestellt. Bei der Bereitstellung des Mandanten benennen Sie ein Azure-Abonnement in Ihrer Azure-Registrierung. Diesem Azure-Abonnement werden die Kosten von HANA (große Instanz) in Rechnung gestellt. Diese Mandanten weisen eine 1:1-Beziehung mit dem Azure-Abonnement auf. In einem Netzwerk ist der Zugriff auf eine Einheit von HANA (große Instanz), die in einem Mandanten in einer Azure-Region bereitgestellt ist, über verschiedene, zu unterschiedlichen Azure-Abonnements gehörende virtuelle Netzwerke möglich. Diese Azure-Abonnements müssen zur selben Azure-Registrierung gehören. 

Wie bei VMs wird SAP HANA in Azure (große Instanzen) in mehreren Azure-Regionen angeboten. Funktionen zur Notfallwiederherstellung können wahlweise abonniert werden. Die verschiedenen Stapel für große Instanzen sind innerhalb einer geopolitischen Azure-Region miteinander verbunden. Umfelder von HANA (große Instanz) in „USA, Westen“ und „USA, Osten“ sind beispielsweise zur Replikation zwecks Notfallwiederherstellung über eine dedizierte Netzwerkverbindung verbunden. 

Ebenso, wie Sie bei Azure Virtual Machines zwischen verschiedenen VM-Typen wählen können, stehen Ihnen verschiedene SKUs von HANA (große Instanz) zur Auswahl, die auf verschiedene Workloadtypen von SAP HANA zugeschnitten sind. Für variierende Workloads wendet SAP basierend auf den Intel-Prozessorgenerationen ein geeignetes Verhältnis zwischen Arbeitsspeicher und Prozessorsockets an. Die folgende Tabelle zeigt die angebotenen SKU-Typen.

Sie finden verfügbare SKUs unter [Verfügbare SKUs für HLI](hana-available-skus.md).

**Nächste Schritte**
- Lesen Sie [SAP HANA-Netzwerkarchitektur (große Instanzen)](hana-network-architecture.md).
