---
title: Einrichten von Message Passing Interface für HPC – Azure Virtual Machines | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie MPI für HPC in Azure einrichten.
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 08/06/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 08d952738a085aa6ed814668ece898f7460b4f33
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963741"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Einrichten von Message Passing Interface für HPC

[Message Passing Interface (MPI)](https://en.wikipedia.org/wiki/Message_Passing_Interface) ist eine offene Bibliothek und der De-facto-Standard für die Parallelisierung von verteiltem Arbeitsspeicher. Sie wird für eine Vielzahl von HPC-Workloads verwendet. HPC-Workloads auf den [RDMA-fähigen](../../sizes-hpc.md#rdma-capable-instances) VMs der [H-Serie](../../sizes-hpc.md) und der [N-Serie](../../sizes-gpu.md) können MPI verwenden, um über das InfiniBand-Netzwerk zu kommunizieren, das sich durch niedrige Latenz und hohe Bandbreite auszeichnet.

Die SR-IOV-fähigen VM-Größen (HBv2, HB, HC, NCv3, NDv2) in Azure ermöglichen die Verwendung nahezu jeder Variante von MPI mit Mellanox OFED. Auf nicht SR-IOV-fähigen virtuellen Computern verwenden unterstützte MPI-Implementierungen die Microsoft Network Direct-Schnittstelle (ND) für die Kommunikation zwischen virtuellen Computern. Daher werden nur die Versionen Microsoft MPI (MS-MPI) 2012 R2 oder höher und Intel MPI 5.x unterstützt. Höhere Versionen (2017, 2018) der Intel MPI-Laufzeitbibliothek sind möglicherweise mit den Azure-RDMA-Treibern kompatibel.

Die VM-Images mit [CentOS-HPC-Version 7.6 oder höher](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) im Marketplace sind für SR-IOV- und [RDMA-fähige VMs](../../sizes-hpc.md#rdma-capable-instances) optimiert und vorab mit den OFED-Treibern für RDMA sowie verschiedenen gängigen MPI-Bibliotheken und Paketen für wissenschaftliches Computing geladen. Damit ermöglichen diese VMs einen sehr einfachen Einstieg.

Obwohl die hier genannten Beispiele für RHEL/CentOS gelten, sind die Schritte allgemein und können für jedes kompatible Linux-Betriebssystem wie Ubuntu (16.04, 18.04, 19.04, 20.04) und SLES (12 SP4 und 15) verwendet werden. Weitere Beispiele für das Einrichten weiterer MPI-Implementierungen auf anderen Distributionen finden Sie im [azhpc-images-Repository](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh).

> [!NOTE]
> Um MPI-Aufträge auf SR-IOV-fähigen VMs ausführen zu können, müssen aus Gründen der Isolation und Sicherheit Partitionsschlüssel auf einem Mandanten eingerichtet werden. Eine ausführliche Anleitung zum Ermitteln der Partitionsschlüsselwerte und zum korrekten Festlegen dieser Werte für einen MPI-Auftrag finden Sie unter [Ermitteln von Partitionsschlüsseln](#discover-partition-keys).

## <a name="ucx"></a>UCX

[Unified Communication X (UCX)](https://github.com/openucx/ucx) ist ein Framework aus Kommunikations-APIs für HPC. Es ist für die MPI-Kommunikation über InfiniBand optimiert und funktioniert mit einer Vielzahl von MPI-Implementierungen wie OpenMPI und MPICH.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="hpc-x"></a>HPC-X

Das [HPC-X-Softwaretoolkit](https://www.mellanox.com/products/hpc-x-toolkit) umfasst UCX und HCOLL.

```bash
HPCX_VERSION="v2.6.0"
HPCX_DOWNLOAD_URL=https://azhpcstor.blob.core.windows.net/azhpc-images-store/hpcx-v2.6.0-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
get --retry-connrefused --tries=3 --waitretry=5 $HPCX_DOWNLOAD_URL
tar -xvf hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
mv hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64 ${INSTALL_PREFIX}
HPCX_PATH=${INSTALL_PREFIX}/hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64
```

Ausführen von HPC-X

```bash
${HPCX_PATH}mpirun -np 2 --map-by ppr:2:node -x UCX_TLS=rc ${HPCX_PATH}/ompi/tests/osu-micro-benchmarks-5.3.2/osu_latency
```

## <a name="openmpi"></a>OpenMPI

Installieren Sie UCX, wie oben beschrieben. HCOLL gehört zum [HPC-X-Softwaretoolkit](https://www.mellanox.com/products/hpc-x-toolkit) und erfordert keine gesonderte Installation.

Installieren Sie OpenMPI aus den im Repository verfügbaren Paketen.

```bash
sudo yum install –y openmpi
```

Erstellen Sie OpenMPI.

```bash
OMPI_VERSION="4.0.3"
OMPI_DOWNLOAD_URL=https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-${OMPI_VERSION}.tar.gz
wget --retry-connrefused --tries=3 --waitretry=5 $OMPI_DOWNLOAD_URL
tar -xvf openmpi-${OMPI_VERSION}.tar.gz
cd openmpi-${OMPI_VERSION}
./configure --prefix=${INSTALL_PREFIX}/openmpi-${OMPI_VERSION} --with-ucx=${UCX_PATH} --with-hcoll=${HCOLL_PATH} --enable-mpirun-prefix-by-default --with-platform=contrib/platform/mellanox/optimized && make -j$(nproc) && make install
```

Führen Sie OpenMPI aus.

```bash
${INSTALL_PREFIX}/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Überprüfen Sie Ihren Partitionsschlüssel, wie weiter oben erwähnt.

## <a name="intel-mpi"></a>Intel MPI

Laden Sie die gewünschte Version von [Intel MPI](https://software.intel.com/mpi-library/choose-download) herunter. Ändern Sie die Umgebungsvariable „I_MPI_FABRICS“ abhängig von der Version. Verwenden Sie `I_MPI_FABRICS=shm:ofa` für Intel MPI 2018 und `I_MPI_FABRICS=shm:ofi` für Intel MPI 2019.

### <a name="non-sr-iov-vms"></a>Nicht-SR-IOV-VMS
Für Nicht-SR-IOV-VMS finden Sie im Folgenden ein Beispiel für das Herunterladen der [kostenlosen Evaluierungsversion](https://registrationcenter.intel.com/en/forms/?productid=1740) der 5.x-Runtime:
```bash
wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
```
Die Installationsschritte finden Sie im [Installationshandbuch für die Intel MPI-Bibliothek](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).
Optional können Sie ptrace für Nicht-Debugger-Prozesse ohne Root-Zugriff aktivieren (erforderlich für die neuesten Versionen von Intel MPI).
```bash
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```

### <a name="suse-linux"></a>SUSE Linux
Bei SUSE Linux Enterprise Server-VM-Imageversionen – SLES 12 SP3 für HPC, SLES 12 SP3 für HPC (Premium), SLES 12 SP1 für HPC, SLES 12 SP1 für HPC (Premium), SLES 12 SP4 und SLES 15 – werden die RDMA-Treiber auf der VM installiert, und die Intel MPI-Pakete werden bereitgestellt. Installieren Sie Intel MPI mit dem folgenden Befehl:
```bash
sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
```

## <a name="mpich"></a>MPICH

Installieren Sie UCX, wie oben beschrieben. Erstellen Sie MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=${UCX_PATH} --prefix=${INSTALL_PREFIX} --with-device=ch4:ucx
make -j 8 && make install
```

Führen Sie MPICH aus.

```bash
${INSTALL_PREFIX}/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Überprüfen Sie Ihren Partitionsschlüssel, wie weiter oben erwähnt.

## <a name="mvapich2"></a>MVAPICH2

Erstellen Sie MVAPICH2.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=${INSTALL_PREFIX}
make -j 8 && make install
```

Führen Sie MVAPICH2 aus.

```bash
${INSTALL_PREFIX}/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>Platform MPI (Community Edition)

Installieren Sie die erforderlichen Pakete für Platform MPI.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Führen Sie die Schritte des Installationsprozesses aus.

## <a name="osu-mpi-benchmarks"></a>OSU-MPI-Benchmarks

Laden Sie die [OSU-MPI-Benchmarks](http://mvapich.cse.ohio-state.edu/benchmarks/) herunter, und entpacken Sie sie.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Erstellen Sie Benchmarks unter Verwendung einer bestimmten MPI-Bibliothek:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI-Benchmarks befinden sich im Ordner `mpi/`.


## <a name="discover-partition-keys"></a>Ermitteln von Partitionsschlüsseln

Ermitteln Sie Partitionsschlüssel für die Kommunikation mit anderen VMs im selben Mandanten (Verfügbarkeitsgruppe oder VM-Skalierungsgruppe).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

Der größere der beiden ist der Mandantenschlüssel, der mit MPI verwendet werden muss. Beispiel: Von den folgenden Partitionsschlüsseln muss „0x800b“ mit MPI verwendet werden:

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Verwenden Sie die Partition, bei der es sich nicht um den Standardpartitionsschlüssel (0x7fff) handelt. Für UCX muss das MSB des Partitionsschlüssels gelöscht werden. Legen Sie also beispielsweise „UCX_IB_PKEY“ für „0x800b“ auf „0x000b“ fest.

Beachten Sie außerdem, dass die Partitionsschlüssel unverändert bleiben, solange der Mandant (Verfügbarkeitsgruppe oder VM-Skalierungsgruppe) vorhanden ist. Dies gilt auch, wenn Knoten hinzugefügt oder gelöscht werden. Neue Mandanten erhalten unterschiedliche Partitionsschlüssel.


## <a name="set-up-user-limits-for-mpi"></a>Einrichten von Benutzergrenzwerten für MPI

Richten Sie Benutzergrenzwerte für MPI ein.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```

## <a name="set-up-ssh-keys-for-mpi"></a>Einrichten von SSH-Schlüsseln für MPI

Richten Sie SSH-Schlüssel für MPI-Typen ein, bei denen dies erforderlich ist.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

In der obigen Syntax wird von einem gemeinsam genutzten Basisverzeichnis ausgegangen. Andernfalls muss das SSH-Verzeichnis an jeden Knoten kopiert werden.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [InfiniBand-fähigen](../../sizes-hpc.md#rdma-capable-instances) VMs der [H-Serie](../../sizes-hpc.md) und der [N-Serie](../../sizes-gpu.md).
- Sehen Sie sich die [Übersicht über virtuelle Computer der HB-Serie](hb-series-overview.md) und die [Übersicht über virtuelle Computer der HC-Serie](hc-series-overview.md) an, um zu erfahren, wie Sie Workloads optimal konfigurieren, um die bestmögliche Leistung und Skalierbarkeit zu erzielen.
- Informieren Sie sich in den [Tech Community-Blogs zu Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute) über die neuesten Ankündigungen, und machen Sie sich mit einigen HPC-Beispielen und Ergebnissen vertraut.
- Eine allgemeinere Architekturübersicht zur Ausführung von HPC-Workloads finden Sie unter [High Performance Computing (HPC) in Azure](/azure/architecture/topics/high-performance-computing/).
