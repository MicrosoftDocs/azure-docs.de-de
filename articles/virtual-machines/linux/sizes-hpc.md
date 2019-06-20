---
title: Größen von virtuellen Azure Linux-Computern – HPC | Microsoft-Dokumentation
description: Auflistung der verschiedenen verfügbaren Größen für virtuelle Linux HPC-Computer (High Performance Computing) in Azure. Dieser Artikel listet Informationen zur Anzahl von vCPUs, Datenträgern und Netzwerkschnittstellenkarten sowie zum Speicherdurchsatz und zur Netzwerkbandbreite für Größen dieser Serie auf.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: 44b965bd60d976d4d28dc5e31d78a1c838d4ee02
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64704680"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>High Performance Computing-VM-Größen

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Nur Intel MPI 5.x-Versionen werden unterstützt.

> [!NOTE]
> Höhere Versionen (2017, 2018) der Intel MPI-Laufzeitbibliothek sind möglicherweise mit den Azure-Linux-RDMA-Treibern kompatibel.

### <a name="distributions"></a>Verteilungen
 
Stellen Sie eine computeintensive VM über eines der Images im Azure Marketplace bereit, das RDMA-Konnektivität unterstützt:
  
* **Ubuntu** – Ubuntu Server 16.04 LTS. Konfigurieren Sie RDMA-Treiber auf der VM, und registrieren Sie sich bei Intel, um Intel MPI herunterzuladen:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **SUSE Linux Enterprise Server** – SLES 12 SP3 for HPC, SLES 12 SP3 for HPC (Premium), SLES 12 SP1 for HPC, SLES 12 SP1 for HPC (Premium). Auf der VM werden RDMA-Treiber installiert und Intel MPI-Pakete verteilt. Installieren Sie MPI mit dem folgenden Befehl:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
    
* **CentOS-basiertes HPC** – CentOS-basiertes HPC der Version 6.5 oder höher (für die H-Reihe wird Version 7.1 oder höher empfohlen). Auf der VM sind RDMA-Treiber und Intel MPI 5.1 installiert.  
 
  > [!NOTE]
  > Bei den CentOS-basierten HPC-Images sind Kernel-Updates in der **yum** -Konfigurationsdatei deaktiviert. Der Grund: Die Linux RDMA-Treiber werden als RPM-Paket verteilt, und Treiberupdates funktionieren möglicherweise nicht, wenn der Kernel aktualisiert wird.
  > 
 
### <a name="cluster-configuration-options"></a>Konfigurationsoptionen für Cluster

Azure bietet mehrere Optionen zum Erstellen von Clustern von Linux-HPC-VMs, die über das RDMA-Netzwerk kommunizieren können, einschließlich der folgenden: 

* **Virtuelle Computer**: Stellen Sie die RDMA-fähigen HPC-VMs in der gleichen Verfügbarkeitsgruppe bereit (wenn Sie das Azure Resource Manager-Bereitstellungsmodell verwenden). Stellen Sie die VMs bei Verwendung des klassischen Bereitstellungsmodells im gleichen Clouddienst bereit. 

* **VM-Skalierungsgruppen**: Stellen Sie bei Verwendung einer VM-Skalierungsgruppe sicher, dass Sie die Bereitstellung auf eine einzelne Platzierungsgruppe beschränken. Legen Sie z. B. in einer Resource Manager-Vorlage die Eigenschaft `singlePlacementGroup` auf `true` fest. 

* **Azure CycleCloud**: Erstellen Sie in [Azure CycleCloud](/azure/cyclecloud/) einen HPC-Cluster zum Ausführen von MPI-Aufträgen auf Linux-Knoten.

* **Azure Batch**: Erstellen Sie einen [Azure Batch](/azure/batch/)-Pool zum Ausführen von MPI-Workloads auf Linux-Computeknoten. Weitere Informationen finden Sie unter [Verwenden RDMA-fähiger oder GPU-fähiger Instanzen in Batch-Pools](../../batch/batch-pool-compute-intensive-sizes.md). Informationen zum Ausführen containerbasierter Workloads in Batch finden Sie im Projekt [Batch Shipyard](https://github.com/Azure/batch-shipyard).

* **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) unterstützt das Ausführen von mehreren Linux-Distributionen auf Computeknoten, die auf RDMA-fähigen virtuellen Azure-Computern bereitgestellt und von einem Windows Server-Hauptknoten verwaltet werden. Eine Beispielbereitstellung finden Sie unter [Erstellen eines HPC Pack Linux RDMA-Clusters in Azure](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

Abhängig von dem von Ihnen ausgewählten Clusterverwaltungstool sind für die Ausführung von MPI-Aufträgen möglicherweise zusätzliche Systemkonfigurationsschritte erforderlich. Beispielsweise müssen Sie in einem Cluster von virtuellen Computern möglicherweise eine Vertrauensstellung zwischen den Clusterknoten herstellen, indem Sie SSH-Schlüssel generieren oder eine SSH-Vertrauensstellung ohne Kennwort einrichten.

### <a name="network-topology-considerations"></a>Überlegungen zur Netzwerktopologie
* Auf RDMA-fähigen virtuellen Linux-Computern in Azure ist „Eth1“ für RDMA-Netzwerkdatenverkehr reserviert. Ändern Sie keine Eth1-Einstellungen oder anderen Informationen in der Konfigurationsdatei, die sich auf dieses Netzwerk beziehen. „Eth0“ ist für den regulären Azure-Netzwerkverkehr reserviert.

* Das RDMA-Netzwerk in Azure reserviert sich den Adressbereich 172.16.0.0/16. 




## <a name="other-sizes"></a>Andere Größen
- [Allgemeiner Zweck](sizes-general.md)
- [Computeoptimiert](sizes-compute.md)
- [Arbeitsspeicheroptimiert](sizes-memory.md)
- [Speicheroptimiert](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Vorherige Generationen](sizes-previous-gen.md)

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen dazu, wie Sie mit [Azure-Computeeinheiten (ACU)](acu.md) die Computeleistung von Azure-SKUs vergleichen können.




