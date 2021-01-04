---
title: Konfiguration und Optimierung von InfiniBand-fähigen Azure-VMs der H- und N-Serie
description: Informieren Sie sich über das Konfigurieren und Optimieren der InfiniBand-fähigen virtuellen Computer der H-Serie und der N-Serie für HPC.
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 10/23/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: b1686b08608e4f1c49cd918e86e8149f0fe2a21c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963367"
---
# <a name="configure-and-optimize-vms"></a>Konfigurieren und Optimieren von virtuellen Computern

In diesem Artikel werden bekannte Verfahren zum Konfigurieren und Optimieren der InfiniBand-fähigen VMs der [H-Serie](../../sizes-hpc.md) und [N-Serie](../../sizes-gpu.md) für HPC beschrieben.

## <a name="vm-images"></a>VM-Images
Auf InfiniBand-fähigen virtuellen Computern sind die entsprechenden Treiber erforderlich, um RDMA zu aktivieren. Unter Linux werden die CentOS-HPC-VM-Images im Marketplace mit den entsprechenden Treibern vorkonfiguriert bereitgestellt. Die Ubuntu-VM-Images können mit den richtigen Treibern mit den [hier folgenden Anweisungen](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351) konfiguriert werden. Außerdem sollten [benutzerdefinierte VM-Images](../../linux/tutorial-custom-images.md) mit den entsprechenden Treibern und Konfigurationen erstellt und wiederholt wiederverwendet werden.

> [!NOTE]
> Bei GPU-fähigen VMs der [N-Serie](../../sizes-gpu.md) sind zusätzlich die entsprechenden GPU-Treiber erforderlich, die über die [VM-Erweiterungen](../../extensions/hpccompute-gpu-linux.md) oder [manuell](../../linux/n-series-driver-setup.md) hinzugefügt werden können. Bei einigen VM-Images im Marketplace sind auch die Nvidia-GPU-Treiber vorinstalliert.

### <a name="centos-hpc-vm-images"></a>CentOS-HPC-VM-Images

#### <a name="non-sr-iov-enabled-vms"></a>SR-IOV nicht unterstützende VMs
Für [RDMA-fähige VMs](../../sizes-hpc.md#rdma-capable-instances), die SR-IOV nicht unterstützen, ist die CentOS-HPC-Version 6.5 oder höher bis zu 7.5 im Marketplace geeignet. Für [virtuelle Computer der H16-Serie](../../h-series.md) werden z. B. die Versionen 7.1 bis 7.5 empfohlen. Bei diesen VM-Images sind die Network Direct-Treiber für RDMA und Intel MPI Version 5.1 vorab geladen.

> [!NOTE]
> Bei den CentOS-basierten HPC-Images für SR-IOV nicht unterstützende VMs sind Kernel-Updates in der **yum**-Konfigurationsdatei deaktiviert. Der Grund: Die Network Direct-Linux RDMA-Treiber werden als RPM-Paket verteilt, und Treiberupdates funktionieren möglicherweise nicht, wenn der Kernel aktualisiert wird.

#### <a name="sr-iov-enabled-vms"></a>SR-IOV unterstützende VMs
  Für [RDMA-fähige VMs](../../sizes-hpc.md#rdma-capable-instances), die SR-IOV unterstützen, ist die [CentOS-HPC-Version 7.6 oder höher](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) im Marketplace geeignet. Diese VM-Images sind optimiert und vorab mit den OFED-Treibern für RDMA sowie verschiedenen gängigen MPI-Bibliotheken und Paketen für wissenschaftliches Computing geladen. Damit bieten diese VMs einen sehr einfachen Einstieg.

  Ein Beispiel für Skripts, die bei der Erstellung der VM-Images CentOS-HPC Version 7.6 und höher aus einem Basis-CentOS-Marketplace-Image verwendet werden, finden Sie im [Repository „azhpc-images“](https://github.com/Azure/azhpc-images/tree/master/centos).
  
  > [!NOTE] 
  > Die neuesten Azure HPC Marketplace-Images sind Mellanox OFED 5.1 und höher, die keine ConnectX3-Pro InfiniBand-Karten unterstützen. Für SR-IOV aktivierte VM-Größen der N-Serie mit FDR InfiniBand (z. B. NCv3) können die folgenden CentOS-HPC-VM-Imageversionen oder niedrigere Versionen verwenden:
  >- OpenLogic:CentOS-HPC:7.6:7.6.2020062900
  >- OpenLogic:CentOS-HPC:7_6gen2:7.6.2020062901
  >- OpenLogic:CentOS-HPC:7.7:7.7.2020062600
  >- OpenLogic:CentOS-HPC:7_7-gen2:7.7.2020062601
  >- OpenLogic:CentOS-HPC:8_1:8.1.2020062400
  >- OpenLogic:CentOS-HPC:8_1-gen2:8.1.2020062401


### <a name="rhelcentos-vm-images"></a>RHEL/CentOS-VM-Images
RHEL- oder CentOS-basierte Nicht-HPC-VM-Images im Marketplace können für die Verwendung auf SR-IOV-aktivierten [RDMA-fähigen VMs](../../sizes-hpc.md#rdma-capable-instances) konfiguriert werden. Informieren Sie sich ausführlicher über die [Aktivierung von InfiniBand](enable-infiniband.md) und [MPI-Einrichtung](setup-mpi.md) auf den VMs.

  Ein Beispiel für Skripts, die bei der Erstellung der VM-Images CentOS-HPC Version 7.6 und höher aus einem Basis-CentOS-Marketplace-Image verwendet werden, finden Sie im [Repository „azhpc-images“](https://github.com/Azure/azhpc-images/tree/master/centos).
  
  > [!NOTE]
  > Ab Mellanox OFED 5.1 werden keine ConnectX3-Pro-InfiniBand-Karten auf für SR-IOV aktivierten VM-Größen der N-Serie mit FDR-InfiniBand (z. B. NCv3) unterstützt. Verwenden Sie LTS Mellanox OFED ab Version 4.9-0.1.7.0 auf den VMs der N-Serie mit ConnectX3-Pro-Karten. Ausführlichere Informationen finden Sie [hier](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed).

### <a name="ubuntu-vm-images"></a>Ubuntu-VM-Images
Die VM-Images von Ubuntu Server 16.04 LTS, 18.04 LTS und 20.04 LTS im Marketplace werden sowohl für SR-IOV unterstützende als auch für SR-IOV nicht unterstützende [RDMA-fähige VMS](../../sizes-hpc.md#rdma-capable-instances) unterstützt. Informieren Sie sich ausführlicher über die [Aktivierung von InfiniBand](enable-infiniband.md) und [MPI-Einrichtung](setup-mpi.md) auf den VMs.

  Ein Beispiel für Skripts, die bei der Erstellung von Ubuntu 18.04 LTS-basierten HPC-VM-Images verwendet werden können, finden Sie im [Repository „azhpc-images“](https://github.com/Azure/azhpc-images/tree/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc).

### <a name="suse-linux-enterprise-server-vm-images"></a>SUSE Linux Enterprise Server-VM-Images
SLES 12 SP3 for HPC-, SLES 12 SP3 for HPC (Premium)-, SLES 12 SP1 for HPC-, SLES 12 SP1 for HPC (Premium)-, SLES 12 SP4- und SLES 15-VM-Images im Marketplace werden unterstützt. Bei diesen VM-Images sind die Network Direct-Treiber für RDMA und Intel MPI Version 5.1 vorab geladen. Informieren Sie sich über das [Einrichten von MPI](setup-mpi.md) auf den VMs.

## <a name="optimize-vms"></a>Optimieren von VMs

Im folgenden finden Sie einige optionale Optimierungseinstellungen für eine verbesserte Leistung des virtuellen Computers.

### <a name="update-lis"></a>Aktualisieren von LIS

Sofern für Funktionalität oder Leistung erforderlich, können [LIS-Treiber (Linux Integration Services)](../../linux/endorsed-distros.md) auf unterstützten Betriebssystemdistributionen installiert oder aktualisiert werden. Dies gilt insbesondere für die Bereitstellung mit einem benutzerdefinierten Image oder einer älteren Betriebssystemversion wie CentOS/RHEL 6.x oder einer früheren Version von 7.x.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### <a name="reclaim-memory"></a>Freigeben von Arbeitsspeicher

Steigern Sie die Leistung durch die automatische Freigabe von Arbeitsspeicher, um den Remotezugriff auf den Speicher zu vermeiden.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

So legen Sie fest, dass diese Einstellung auch nach VM-Neustarts gilt:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

### <a name="disable-firewall-and-selinux"></a>Deaktivieren von Firewall und SELinux

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

### <a name="disable-cpupower"></a>Deaktivieren von cpupower

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

### <a name="configure-walinuxagent"></a>Konfigurieren von WALinuxAgent

```bash
sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
```
Optional kann der WALinuxAgent zur maximalen Verfügbarkeit der VM-Ressource für die HPC-Workload als Vor-Auftrags-Schritt deaktiviert und nach dem Auftrag wieder aktiviert werden.


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [InfiniBand-Aktivierung](enable-infiniband.md) auf der InfiniBand-fähigen [H-Serie](../../sizes-hpc.md) und [N-Serie](../../sizes-gpu.md) virtueller Computer.
- Erfahren Sie mehr über die Installation verschiedener [unterstützter MPI-Bibliotheken](setup-mpi.md) sowie ihre optimale Konfiguration auf den VMs.
- Sehen Sie sich die [Übersicht über virtuelle Computer der HB-Serie](hb-series-overview.md) und die [Übersicht über virtuelle Computer der HC-Serie](hc-series-overview.md) an, um zu erfahren, wie Sie Workloads optimal konfigurieren, um die bestmögliche Leistung und Skalierbarkeit zu erzielen.
- Informieren Sie sich in den [Tech Community-Blogs zu Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute) über die neuesten Ankündigungen, und machen Sie sich mit einigen HPC-Beispielen und Ergebnissen vertraut.
- Eine allgemeinere Architekturübersicht zur Ausführung von HPC-Workloads finden Sie unter [High Performance Computing (HPC) in Azure](/azure/architecture/topics/high-performance-computing/).
