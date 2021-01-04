---
title: Ausführen einer SQL Server-VM auf einem Azure Dedicated Host
description: Erfahren Sie, wie Sie eine SQL Server-VM auf einem Azure Dedicated Host ausführen.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 957e458bf21eb4a95b1faba1daf0459271648310
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358689"
---
# <a name="run-sql-server-vm-on-an-azure-dedicated-host"></a>Ausführen einer SQL Server-VM auf einem Azure Dedicated Host 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In diesem Artikel werden die Besonderheiten für die Verwendung einer SQL Server-VM mit einem [Azure Dedicated Host](../../../virtual-machines/dedicated-hosts.md) erläutert. Weitere Informationen zum Azure Dedicated Host bzw. zum Dienst Azure Dedicated Host finden Sie im Blogbeitrag [Einführung von Azure Dedicated Host](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/). 

## <a name="overview"></a>Übersicht
[Azure Dedicated Host](../../../virtual-machines/dedicated-hosts.md) ist ein Dienst zum Bereitstellen physischer Server, die virtuelle Computer hosten können und ausschließlich für ein Azure-Abonnement reserviert sind. Dedizierte Hosts sind die in Microsofts Rechenzentren verwendeten physischen Server, die als Ressource bereitgestellt werden. Sie können dedizierte Hosts in einer Region, einer Verfügbarkeitszone und einer Fehlerdomäne bereitstellen. Anschließend können Sie VMs direkt in Ihren bereitgestellten Hosts platzieren, und zwar in einer beliebigen Konfiguration, die Ihren Anforderungen am besten entspricht.

## <a name="limitations"></a>Einschränkungen

- Nicht alle VM-Serien werden auf dedizierten Hosts unterstützt, und die Verfügbarkeit der VM-Serien variiert je nach Region. Weitere Informationen finden Sie unter [Übersicht über dedizierte Azure-Hosts](../../../virtual-machines/dedicated-hosts.md).

## <a name="licensing"></a>Lizenzierung

Sie können zwischen zwei verschiedenen Lizenzierungsoptionen wählen, wenn Sie Ihre SQL Server-VM auf einem Azure Dedicated Host platzieren. 

  - **SQL VM licensing** (Lizenzierung virtueller SQL-Computer): Dies ist die vorhandene Lizenzierungsoption, bei der Sie jede SQL Server-VM-Lizenz einzeln bezahlen. 
  - **Dedicated host licensing** (Lizenzierung eines dedizierten Hosts): Das neue Lizenzierungsmodell, das für Azure Dedicated Host verfügbar ist und in dem SQL Server-Lizenzen gebündelt sind und auf Hostebene bezahlt werden. 


Optionen auf Hostebene für das Verwenden vorhandener SQL Server-Lizenzen: 
  - SQL Server Enterprise Edition-Azure-Hybridvorteil (AHB)
    - Verfügbar für Kunden mit SA oder Abonnement.
    - Lizenzieren Sie alle verfügbaren physischen Kerne, und profitieren Sie von unbegrenzter Virtualisierung (bis zur maximalen Anzahl von vCPUs, die vom Host unterstützt wird).
        - Weitere Informationen zum Anwenden des AHB auf einen Azure Dedicated Host finden Sie in [Häufig gestellte Fragen zum Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/faq/). 
  - SQL Server-Lizenzen, die vor dem 1. Oktober erworben wurden
      - SQL Server Enterprise Edition hat beide Lizenzoptionen: Hostebene und VM-bezogen. 
      - Für SQL Server Standard Edition ist nur die Lizenzoption „VM-bezogen“ verfügbar. 
          - Ausführliche Informationen finden Sie unter [Produktbestimmungen von Microsoft](https://www.microsoft.com/licensing/product-licensing/products). 
  - Wenn keine dedizierte Hostebenenoption für SQL Server ausgewählt ist, kann SQL Server-Azure-Hybridvorteil auf der Ebene einzelner virtueller Computer ausgewählt werden, genau wie bei mehrinstanzenfähigen virtuellen Computern.



## <a name="provisioning"></a>Bereitstellung  
Das Bereitstellen einer SQL Server-VM auf dem dedizierten Host unterscheidet sich nicht von der Vorgehensweise für andere Azure-VMs. Sie können dazu [Azure PowerShell](../../../virtual-machines/windows/dedicated-hosts-powershell.md), das [Azure-Portal](../../../virtual-machines/dedicated-hosts-portal.md) und die [Azure-Befehlszeilenschnittstelle](../../../virtual-machines/linux/dedicated-hosts-cli.md) verwenden.

Das Hinzufügen einer vorhandenen SQL Server-VM zu dem dedizierten Host erfordert Ausfallzeit, wirkt sich jedoch nicht auf Daten aus und bewirkt keinen Datenverlust. Trotzdem sollten vor dem Verschieben alle Datenbanken, einschließlich der Systemdatenbanken, gesichert werden.

## <a name="virtualization"></a>Virtualisierung 

Einer der Vorteile eines dedizierten Hosts ist die unbegrenzte Virtualisierung. Beispielsweise können Sie über Lizenzen für 64 virtuelle Kerne verfügen, können den Host aber so konfigurieren, dass er 128 virtuelle Kerne hat. Sie erhalten also die doppelte Anzahl von virtuellen Kernen, zahlen aber nur für die Hälfte der SQL Server-Lizenzen. 

Da dieser Host Ihr Host ist, können Sie die Virtualisierung mit einem Verhältnis von 1:2 festlegen. 

## <a name="faq"></a>Häufig gestellte Fragen

**F: Wie funktioniert der Azure-Hybridvorteil für Windows Server- bzw. SQL Server-Lizenzen mit Azure Dedicated Host?**

A: Kunden können den Wert Ihrer vorhandenen Windows Server- und SQL Server Lizenzen mit Software Assurance oder qualifizierenden Abonnementlizenzen verwenden, um einen verringerten Preis für Azure Dedicated Host mit Azure-Hybridvorteil zu zahlen. Windows Server Datacenter und SQL Server Enterprise Edition-Kunden erhalten eine unbegrenzte Virtualisierung (Bereitstellen so vieler virtueller Windows Server-Computer wie auf dem Host möglich, abhängig von der physischen Kapazität des zugrunde liegenden Servers), wenn sie den gesamten Host lizenzieren und Azure-Hybridvorteil verwenden.  Für alle Windows Server- und SQL Server-Workloads in Azure Dedicated Host können zudem erweiterte Sicherheitsupdates für Windows Server und SQL Server 2008/R2 ohne zusätzliche Kosten in Anspruch genommen werden. 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln: 

* [Übersicht über SQL Server auf einem virtuellen Windows-Computer](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Häufig gestellte Fragen zu SQL Server auf virtuellen Windows-Computern](frequently-asked-questions-faq.md)
* [Preisinformationen für SQL Server auf virtuellen Windows-Computern](pricing-guidance.md)
* [SQL Server auf Windows-VMs – Versionshinweise](doc-changes-updates-release-notes.md)