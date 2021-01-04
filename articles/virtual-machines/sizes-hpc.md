---
title: 'Azure-VM-Größen: HPC | Microsoft-Dokumentation'
description: Liste der verschiedenen verfügbaren Größen für virtuelle HPC-Computer (High Performance Computing) in Azure. Dieser Artikel listet Informationen zur Anzahl von vCPUs, Datenträgern und Netzwerkschnittstellenkarten sowie zum Speicherdurchsatz und zur Netzwerkbandbreite für Größen dieser Serie auf.
author: vermagit
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/03/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 21b1004a347dec3a7f2a6460d8b853350bf36ff0
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571044"
---
# <a name="high-performance-computing-vm-sizes"></a>High-Performance Computing (HPC): VM-Größen

Virtuelle Azure-Computer (Azure-VMs) der H-Serie bieten eine herausragende Leistung, Skalierbarkeit und Kosteneffizienz für verschiedene HPC-Workloads in der Praxis.

VMs der [HBv2-Serie](hbv2-series.md) sind für Anwendungen mit hohen Anforderungen an die Speicherbandbreite optimiert, z. B. Strömungssimulationen, Finite-Element-Analysen oder Lagerstättensimulationen. HBv2-VMs bieten 120 AMD-Prozessorkerne des Modells EPYC 7742, 4 GB RAM pro CPU-Kern und kein gleichzeitiges Multithreading. Jede HBv2-VM bietet eine Speicherbandbreite von bis zu 340 Gbit/s und eine FP64-Computeleistung von bis zu 4 TeraFLOP.

HBv2-VMs unterstützen Mellanox HDR InfiniBand mit 200 GBit/s, und VMs der Serien HB und HC unterstützen Mellanox EDR InfiniBand mit 100 GBit/s. Jeder dieser VM-Typen ist für eine optimierte und konsistente RDMA-Leistung in einer FAT-Struktur ohne Blocks verbunden. HBv2-VMs unterstützen adaptives Routing und DCT (Dynamic Connected Transport, zusätzlich zum standardmäßigen RC- und UD-Transport). Diese Features verbessern die Anwendungsleistung, Skalierbarkeit und Konsistenz, und ihre Verwendung wird dringend empfohlen.

VMs der [HB-Serie](hb-series.md) sind für Anwendungen mit hohen Anforderungen an die Speicherbandbreite optimiert, z. B. Strömungssimulationen, explizite Finite-Elemente-Analysen oder Wettermodelle. HB-VMs bieten 60 AMD-Prozessorkerne des Modells EPYC 7551, 4 GB RAM pro CPU-Kern und kein Hyperthreading. Die AMD EPYC-Plattform bietet eine Speicherbandbreite von mehr als 260 GB/Sekunde.

VMs der [HC-Serie](hc-series.md) sind für Anwendungen mit hohen Anforderungen an die Rechenleistung optimiert, z. B. implizite Finite-Elemente-Analysen, Molekulardynamik oder Chemoinformatik. HC-VMs bieten 44 Intel-Prozessorkerne des Modells Xeon Platinum 8168, 8 GB RAM pro CPU-Kern und kein Hyperthreading. Die Intel Xeon Platinum-Plattform unterstützt Intels umfangreiches Ökosystem von Softwaretools, etwa die Intel Math Kernel Library.

VMs der [H-Serie](h-series.md) sind für Anwendungen optimiert, für die Anforderungen hinsichtlich hoher CPU-Frequenzen oder großem Arbeitsspeicher pro Kern gelten. Virtuelle Computer der H-Serie bieten 8 oder 16 Intel-Prozessorkerne des Modells Xeon E5 2667 v3, 7 oder 14 GB RAM pro CPU-Kern und kein Hyperthreading. Die H-Serie unterstützt Mellanox EDR InfiniBand mit 56 GBit/s in einer FAT-Strukturkonfiguration ohne Blocks für einheitliche RDMA-Leistung. Virtuelle Computer der H-Serie unterstützen Intel MPI 5.x und MS-MPI.

> [!NOTE]
> Die Außerbetriebnahme von [A8- bis A11-VMs](./sizes-previous-gen.md#a-series---compute-intensive-instances) ist für März 2021 geplant. Weitere Informationen finden Sie im [HPC-Migrationsleitfaden](https://azure.microsoft.com/resources/hpc-migration-guide/).

## <a name="rdma-capable-instances"></a>RDMA-fähige Instanzen

Die meisten der HPC-VM-Größen (HBv2, HB, HC, H16r, H16mr, A8 und A9) verfügen über eine Netzwerkschnittstelle für RDMA-Verbindungen (Remote Direct Memory Access). Ausgewählte Größen der [N-Serie](./nc-series.md) mit dem Zusatz „r“ (ND40rs_v2, ND24rs, NC24rs_v3, NC24rs_v2 und NC24r) sind ebenfalls RDMA-fähig. Diese Schnittstelle steht zusätzlich zur standardmäßigen Azure-Ethernet-Netzwerkschnittstelle in anderen VM-Größen zur Verfügung.

Mithilfe dieser Schnittstelle können die RDMA-fähigen Instanzen über ein InfiniBand-Netzwerk (IB) kommunizieren, das mit HDR-Raten für virtuelle Computer der Größen HBv2, EDR-Raten für virtuelle Computer der Größen HB, HC, NDv2 sowie mit FDR-Raten für virtuelle Computer der Größen H16r und H16mr sowie andere für RDMA-fähige virtuelle Computer der N-Serie und mit QDR-Raten für virtuelle Computer der Größe A8 und A9 betrieben wird. Mit diesen RDMA-Funktionen können Skalierbarkeit und Leistung von bestimmten MPI-Anwendungen (Message Passing Interface) gesteigert werden.

> [!NOTE]
> In Azure HPC gibt es zwei Klassen von virtuellen Computern, je nachdem, ob sie SR-IOV-fähig für InfiniBand sind. Derzeit sind fast alle für RDMA oder InfiniBand geeigneten VMs der neueren Generation in Azure SR-IOV-fähig – mit Ausnahme von H16r, H16mr, NC24r, A8 und A9.
> RDMA kann nur über das IB-Netzwerk (InfiniBand) verwendet werden und wird für alle RDMA-fähigen virtuellen Computer unterstützt.
> „IP over IB“ wird nur auf den SR-IOV-fähigen virtuellen Computern unterstützt.
> RDMA kann nicht über das Ethernet-Netzwerk verwendet werden.

- **Betriebssystem**: Linux wird für HPC-VMs sehr gut unterstützt, verbreitet sind Distributionen wie CentOS, RHEL, Ubuntu, SUSE. Was die Windows-Unterstützung betrifft, so wird Windows Server 2016 oder höher auf allen virtuellen Computern der HPC-Serie unterstützt. Windows Server 2012 R2 und Windows Server 2012 werden auch auf virtuellen Computern unterstützt, die nicht SR-IOV-fähig sind (H16r, H16mr, A8 und A9). Beachten Sie, dass [Windows Server 2012 R2 auf HBv2 und anderen VMs mit mehr als 64 (virtuellen oder physischen) Kernen nicht unterstützt wird](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows). Unter [Optimierung für Linux](./workloads/hpc/configure.md) finden Sie eine Liste der unterstützten VM-Images im Marketplace und Informationen dazu, wie diese entsprechend konfiguriert werden.

- **InfiniBand und Treiber**: Auf InfiniBand-fähigen virtuellen Computern sind die entsprechenden Treiber erforderlich, um RDMA zu aktivieren. Unter Linux werden sowohl für SR-IOV-fähige als auch für nicht SR-IOV-fähige VMs die CentOS-HPC-VM-Images im Marketplace mit den entsprechenden Treibern vorkonfiguriert bereitgestellt. Die Ubuntu-VM-Images können mit den richtigen Treibern mit den [hier folgenden Anweisungen](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351) konfiguriert werden. Weitere Informationen zu gebrauchsfertigen Betriebssystemimages für Linux-VMs finden Sie unter [Optimierung für Linux](./workloads/hpc/configure.md).

   Unter Linux kann die [InfiniBandDriverLinux-VM-Erweiterung](./extensions/hpc-compute-infiniband-linux.md) verwendet werden, um die Mellanox OFED-Treiber zu installieren und InfiniBand auf den SR-IOV-fähigen VMs der H- und N-Serie zu aktivieren. Weitere Informationen zum Aktivieren von InfiniBand auf RDMA-fähigen VMs finden Sie unter [Aktivieren von InfiniBand mit SR-IOV](./workloads/hpc/enable-infiniband.md).

   Unter Windows installiert die VM-Erweiterung [InfiniBandDriverWindows](./extensions/hpc-compute-infiniband-windows.md) Windows Network Direct-Treiber (auf VMs ohne SR-IOV) oder Mellanox OFED-Treiber (auf VMs mit SR-IOV), um RDMA-Konnektivität herzustellen. In bestimmten Bereitstellungen von A8- und A9-Instanzen wird die Erweiterung HpcVmDrivers automatisch hinzugefügt. Beachten Sie, dass die VM-Erweiterung „HpcVmDrivers“ eingestellt wird. Sie wird nicht mehr aktualisiert.

   Um die VM-Erweiterung einer VM hinzuzufügen, können Sie [Azure PowerShell](/powershell/azure/)-Cmdlets verwenden. Weitere Informationen finden Sie unter [Erweiterungen und Features für virtuelle Computer](./extensions/overview.md). Erweiterungen können auch für virtuelle Computer verwendet werden, die mit dem [klassischen Bereitstellungsmodell](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic) bereitgestellt wurden.

- **MPI**: Die SR-IOV-fähigen VM-Größen in Azure gestatten die Verwendung nahezu jeder Variante von MPI mit Mellanox OFED. Auf nicht SR-IOV-fähigen virtuellen Computern verwenden unterstützte MPI-Implementierungen die Microsoft Network Direct-Schnittstelle (ND) für die Kommunikation zwischen virtuellen Computern. Daher werden nur die Versionen Microsoft MPI (MS-MPI) 2012 R2 oder höher und Intel MPI 5.x unterstützt. Höhere Versionen (2017, 2018) der Intel MPI-Laufzeitbibliothek sind möglicherweise mit den Azure-RDMA-Treibern kompatibel. Details zum Einrichten von MPI (Message Passing Interface) auf HPC-VMs in Azure finden Sie unter [Einrichten von Message Passing Interface für HPC](./workloads/hpc/setup-mpi.md).

- **RDMA-Netzwerkadressbereich:** Für das RDMA-Netzwerk in Azure wird der Adressbereich 172.16.0.0/16 reserviert. Wenn Sie MPI-Anwendungen auf Instanzen ausführen möchten, die in einem virtuellen Azure-Netzwerk bereitgestellt wurden, vergewissern Sie sich, dass der Adressraum des virtuellen Netzwerks sich nicht mit dem RDMA-Netzwerk überschneidet.

## <a name="cluster-configuration-options"></a>Konfigurationsoptionen für Cluster

Azure bietet mehrere Optionen zum Erstellen von Clustern von Windows-HPC-VMs, die über das RDMA-Netzwerk kommunizieren können, einschließlich der folgenden: 

- **Virtuelle Computer**: Stellen Sie die RDMA-fähigen HPC-VMs in derselben Skalierungsgruppe oder Verfügbarkeitsgruppe bereit (wenn Sie das Azure Resource Manager-Bereitstellungsmodell verwenden). Stellen Sie die VMs bei Verwendung des klassischen Bereitstellungsmodells im gleichen Clouddienst bereit.

- **VM-Skalierungsgruppen**: Stellen Sie bei Verwendung einer VM-Skalierungsgruppe sicher, dass Sie die Bereitstellung auf eine einzelne Platzierungsgruppe für InfiniBand-Kommunikation in der Skalierungsgruppe beschränken. Legen Sie z. B. in einer Resource Manager-Vorlage die Eigenschaft `singlePlacementGroup` auf `true` fest. Beachten Sie, dass die maximale Skalierungsgruppengröße, die mit der `singlePlacementGroup`-Eigenschaft auf `true` hochgefahren werden kann, standardmäßig auf 100 VMs begrenzt ist. Wenn Ihre Anforderung hinsichtlich der HPC-Aufträge mehr als 100 virtuelle Computer in einem einzelnen Mandanten umfasst, können Sie eine Erhöhung anfordern [und eine kostenlose Anfrage für den Onlinekundensupport](../azure-portal/supportability/how-to-create-azure-support-request.md) öffnen. Der Grenzwert für die Anzahl der virtuellen Computer in einer einzelnen Skalierungsgruppe kann auf 300 erhöht werden. Beachten Sie, dass bei der Bereitstellung von VMs mit Verfügbarkeitsgruppen der Höchstwert bei 200 VMs pro Verfügbarkeitsgruppe liegt.

- **MPI zwischen virtuellen Computern**: Wenn RDMA (z. B. mithilfe der MPI-Kommunikation) zwischen virtuellen Computern (VMs) erforderlich ist, sollten Sie sicherstellen, dass sich die VMs in derselben VM-Skalierungsgruppe oder Verfügbarkeitsgruppe befinden.

- **Azure CycleCloud**: Erstellen Sie in [Azure CycleCloud](/azure/cyclecloud/) einen HPC-Cluster zum Ausführen von MPI-Aufträgen.

- **Azure Batch**: Erstellen Sie einen [Azure Batch](../batch/index.yml)-Pool, um MPI-Workloads auszuführen. Informationen zur Verwendung rechenintensiver Instanzen zum Ausführen von MPI-Anwendungen mit Azure Batch finden Sie unter [Verwendung von Tasks mit mehreren Instanzen zum Ausführen von MPI-Anwendungen (Message Passing Interface) in Azure Batch](../batch/batch-mpi.md).

- **Microsoft HPC Pack** - [HPC Pack](/powershell/high-performance-computing/overview) enthält eine Laufzeitumgebung für MS-MPI, die das Azure-RDMA-Netzwerk bei der Bereitstellung auf RDMA-fähigen Linux-VMs verwendet. Beispielbereitstellungen finden Sie unter [Einrichten eines Linux-RDMA-Clusters mit HPC Pack zum Ausführen von MPI-Anwendungen](/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="deployment-considerations"></a>Überlegungen zur Bereitstellung

- **Azure-Abonnement:** Um eine größere Anzahl von rechenintensiven Instanzen bereitzustellen, sollten Sie ein Abonnement mit nutzungsbasierter Bezahlung oder andere Kaufoptionen in Erwägung ziehen. Bei Verwendung eines [kostenlosen Azure-Kontos](https://azure.microsoft.com/free/)können Sie nur eine begrenzte Anzahl von Azure-Compute-Kernen nutzen.

- **Preise und Verfügbarkeit:** Diese VM-Größen werden nur im Tarif „Standard“ angeboten. Informationen zur Verfügbarkeit in den Azure-Regionen finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/) .

- **Kontingent Speicherkerne:** Es kann sein, dass Sie das Kontingent für die Speicherkerne in Ihrem Azure-Abonnement anpassen müssen, indem Sie den Standardwert erhöhen. Möglicherweise ist bei Ihrem Abonnement auch die Anzahl von Kernen beschränkt, die in bestimmten VM-Größenkategorien bereitgestellt werden können. In diesem Fall können Sie kostenlos [eine Anfrage an den Onlinekundensupport richten](../azure-portal/supportability/how-to-create-azure-support-request.md) und eine Erhöhung des Kontingents anfordern. (Standardgrenzwerte variieren unter Umständen je nach Abonnementkategorie.)

  > [!NOTE]
  > Wenn Sie einen umfangreichen Kapazitätsbedarf haben, wenden Sie sich an den Azure-Support. Azure-Kontingente sind Angebotsbeschränkungen, keine Kapazitätsgarantien. Unabhängig von Ihrem Kontingent werden nur die tatsächlich verwendeten Kerne in Rechnung gestellt.
  
- **Virtuelles Netzwerk:** Ein [virtuelles Azure-Netzwerk](https://azure.microsoft.com/documentation/services/virtual-network/) ist nicht erforderlich, um die rechenintensiven Instanzen zu verwenden. Für viele Bereitstellungen benötigen Sie jedoch mindestens ein cloudbasiertes virtuelles Azure-Netzwerk oder auch eine Site-to-Site-Verbindung für den Zugriff auf lokale Ressourcen. Erstellen Sie ggf. ein neues virtuelles Netzwerk zum Bereitstellen der Instanzen. Das Hinzufügen rechenintensiver virtueller Computer zu einem virtuellen Netzwerk in einer Affinitätsgruppe wird nicht unterstützt.

- **Größenanpassung:** Aufgrund der speziellen Hardware können Sie die Größe rechenintensiver Instanzen nur innerhalb der gleichen Größenfamilie (H-Serie oder N-Serie) anpassen. So können Sie beispielsweise die Größe eines virtuellen Computers der H-Serie nur auf eine andere Größe der H-Serie festlegen. Für bestimmte virtuelle Computer müssen möglicherweise zusätzliche Überlegungen zur Unterstützung von InfiniBand-Treibern und NVMe-Datenträgern in Betracht gezogen werden.


## <a name="other-sizes"></a>Andere Größen

- [Allgemeiner Zweck](sizes-general.md)
- [Computeoptimiert](sizes-compute.md)
- [Arbeitsspeicheroptimiert](sizes-memory.md)
- [Speicheroptimiert](sizes-storage.md)
- [GPU-optimiert](sizes-gpu.md)
- [Vorherige Generationen](sizes-previous-gen.md)

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über das [Konfigurieren Ihrer VMs](./workloads/hpc/configure.md), [Aktivieren von InfiniBand](./workloads/hpc/enable-infiniband.md), [Einrichten von MPI](./workloads/hpc/setup-mpi.md) und Optimieren von HPC-Anwendungen für Azure unter [High Performance Computing auf InfiniBand-fähigen virtuellen Computern der H- und N-Serie](./workloads/hpc/overview.md).
- Informieren Sie sich in den [Tech Community-Blogs zu Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute) über die neuesten Ankündigungen, und machen Sie sich mit einigen Beispielen und Ergebnissen zu HPC vertraut.
- Eine allgemeinere Architekturübersicht zur Ausführung von HPC-Workloads finden Sie unter [High Performance Computing (HPC) in Azure](/azure/architecture/topics/high-performance-computing/).
