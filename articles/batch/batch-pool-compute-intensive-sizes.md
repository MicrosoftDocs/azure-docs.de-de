---
title: Verwenden rechenintensiver Azure-VMs mit Batch | Microsoft-Dokumentation
description: So nutzen Sie die HPC- und GPU-VM-Größen in Azure Batch-Pools
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/17/2018
ms.author: lahugh
ms.openlocfilehash: 3974be886b57fbf685b211369094edf844d96ab6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60776523"
---
# <a name="use-rdma-or-gpu-instances-in-batch-pools"></a>Verwenden RDMA-fähiger oder GPU-fähiger Instanzen in Batch-Pools

Für die Ausführung bestimmter Batch-Aufträge können Sie die Vorteile von Azure-VM-Größen nutzen, die speziell für umfangreiche Berechnungen entwickelt wurden. Beispiel:

* Wenn Sie [MPI-Workloads](batch-mpi.md) mit mehreren Instanzen ausführen, wählen Sie die H-Serie oder andere Größen aus, die über eine Netzwerkschnittstelle für Remote Direct Memory Access (RDMA) verfügen. Diese Größen stellen für die knotenübergreifende Kommunikation eine Verbindung mit einem InfiniBand-Netzwerk her, um so MPI-Anwendungen zu beschleunigen. 

* Wählen Sie bei Verwendung von CUDA-Anwendungen Größen der N-Serie aus, die NVIDIA Tesla GPU-Karten (Graphics Processing Unit) enthalten.

Dieser Artikel enthält Anweisungen und Anwendungsbeispiele für einige spezielle Größen in Azure für Batch-Pools. Technische Daten und Hintergrundinformationen finden Sie unter:

* Größen von virtuellen HPC-Computern (High Performance Computing) – [Linux](../virtual-machines/linux/sizes-hpc.md), [Windows](../virtual-machines/windows/sizes-hpc.md) 

* GPU-fähige VM-Größen ([Linux](../virtual-machines/linux/sizes-gpu.md), [Windows](../virtual-machines/windows/sizes-gpu.md)) 

> [!NOTE]
> Bestimmte VM-Größen sind in den Regionen, in denen Sie Ihre Batch-Konten erstellen, möglicherweise nicht verfügbar. Informationen zu den verfügbaren Größen finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/regions/services/) und [Auswählen einer VM-Größe für einen Batch-Pool](batch-pool-vm-sizes.md).

## <a name="dependencies"></a>Abhängigkeiten

Die RDMA- oder GPU-Funktionen rechenintensiver Größen in Batch werden nur unter bestimmten Betriebssystemen unterstützt. (Die Liste der unterstützten Betriebssysteme stellt eine Teilmenge der Betriebssysteme dar, die für in diesen Größen erstellte virtuelle Computer unterstützt werden.) Je nachdem, wie Sie Ihren Batch-Pool erstellen, müssen Sie möglicherweise zusätzliche Treiber oder andere Software auf den Knoten installieren oder konfigurieren. In den folgenden Tabellen werden diese Abhängigkeiten zusammengefasst. Einzelheiten finden Sie in den verlinkten Artikeln. Optionen zum Konfigurieren von Batch-Pools finden Sie weiter unten in diesem Artikel.

### <a name="linux-pools---virtual-machine-configuration"></a>Linux-Pools – Konfiguration „Virtueller Computer“

| Größe | Funktion | Betriebssysteme | Erforderliche Software | Pooleinstellungen |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Ubuntu 16.04 LTS oder<br/>CentOS-basierter HPC<br/>(Azure Marketplace) | Intel MPI 5<br/><br/>Linux-RDMA-Treiber | Knotenübergreifende Kommunikation aktivieren, parallele Taskausführung deaktivieren |
| [NC-, NCv2-, NCv3-, NDv2-Serie](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla GPU (je nach Serie) | Ubuntu 16.04 LTS oder<br/>CentOS 7.3 oder 7.4<br/>(Azure Marketplace) | NVIDIA CUDA- oder CUDA Toolkit-Treiber | – | 
| [NV-, NVv2-Serie](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Ubuntu 16.04 LTS oder<br/>CentOS 7.3<br/>(Azure Marketplace) | NVIDIA GRID-Treiber | – |

<sup>*</sup>RDMA-fähige Größen der N-Serie umfassen auch NVIDIA Tesla GPUs.

### <a name="windows-pools---virtual-machine-configuration"></a>Windows-Pools – Konfiguration „Virtueller Computer“

| Größe | Funktion | Betriebssysteme | Erforderliche Software | Pooleinstellungen |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/windows/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Windows Server 2016, 2012 R2 oder<br/>2012 (Azure Marketplace) | Microsoft MPI 2012 R2 oder höher oder<br/> Intel MPI 5<br/><br/>Windows-RDMA-Treiber | Knotenübergreifende Kommunikation aktivieren, parallele Taskausführung deaktivieren |
| [NC-, NCv2-, NCv3-, ND-, NDv2-Serie](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla GPU (je nach Serie) | Windows Server 2016 oder <br/>2012 R2 (Azure Marketplace) | NVIDIA CUDA- oder CUDA Toolkit-Treiber| – | 
| [NV-, NVv2-Serie](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 oder<br/>2012 R2 (Azure Marketplace) | NVIDIA GRID-Treiber | – |

<sup>*</sup>RDMA-fähige Größen der N-Serie umfassen auch NVIDIA Tesla GPUs.

### <a name="windows-pools---cloud-services-configuration"></a>Windows-Pools – Konfiguration „Clouddienst“

> [!NOTE]
> Größen der N-Serie werden in Batch-Pools mit der Clouddienstkonfiguration nicht unterstützt.
>

| Größe | Funktion | Betriebssysteme | Erforderliche Software | Pooleinstellungen |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2016, 2012 R2, 2012 oder<br/>2008 R2 (Gastbetriebssystemfamilie) | Microsoft MPI 2012 R2 oder höher oder<br/>Intel MPI 5<br/><br/>Windows-RDMA-Treiber | Kommunikation zwischen Knoten aktivieren,<br/> parallele Taskausführung deaktivieren |

## <a name="pool-configuration-options"></a>Poolkonfigurationsoptionen

Für das Konfigurieren einer speziellen VM-Größe für Ihren Batch-Pool stehen Ihnen mehrere Optionen zum Installieren der erforderlichen Software oder Treiber zur Verfügung:

* Wählen Sie für Pools in der Konfiguration „Virtueller Computer“ ein vorkonfiguriertes [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/)-VM-Image aus, auf dem Treiber und Software vorinstalliert sind. Beispiele: 

  * [CentOS-based 7.4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview): umfasst RDMA-Treiber und Intel MPI 5.1

  * [Data Science Virtual Machine](../machine-learning/data-science-virtual-machine/overview.md) für Linux oder Windows: umfasst NVIDIA CUDA-Treiber

  * Linux-Images für Batch-Containerworkloads, die auch GPU- und RDMA-Treiber umfassen:

    * [CentOS (mit GPU- und RDMA-Treibern) für Azure Batch-Containerpools](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.centos-container-rdma?tab=Overview)

    * [Ubuntu Server (mit GPU- und RDMA-Treibern) für Azure Batch-Containerpools](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.ubuntu-server-container-rdma?tab=Overview)

* Erstellen Sie ein [benutzerdefiniertes Windows- oder Linux-VM-Image](batch-custom-images.md), auf dem Sie Treiber, Software oder andere erforderliche Einstellungen für die Größe des virtuellen Computers installiert haben. 

* Erstellen Sie ein Batch-[Anwendungspaket](batch-application-packages.md) aus einem gezippten Treiber oder Anwendungsinstallationsprogramm, und konfigurieren Sie Azure Batch für die Bereitstellung des Pakets auf Poolknoten und für die Installation nach der Erstellung der einzelnen Knoten. Wenn das Anwendungspaket beispielsweise ein Installationsprogramm ist, erstellen Sie eine Befehlszeile für die [Startaufgabe](batch-api-basics.md#start-task), mit der die App auf allen Poolknoten im Hintergrund installiert wird. Ziehen Sie die Verwendung eines Anwendungspakets und einer Poolstartaufgabe in Betracht, wenn Ihre Workload von einer bestimmten Treiberversion abhängig ist.

  > [!NOTE] 
  > Die Startaufgabe muss mit erhöhten Rechten (Administrator) ausgeführt werden und die erfolgreiche Ausführung abwarten. Bei lang andauernden Aufgaben erhöht sich die zum Bereitstellen eines Batch-Pools benötigte Zeit.
  >

* [Batch Shipyard](https://github.com/Azure/batch-shipyard) konfiguriert automatisch die GPU- und RDMA-Treiber für die transparente Arbeit mit Containerworkloads in Azure Batch. Batch Shipyard wird vollständig über Konfigurationsdateien gesteuert. Es gibt eine Vielzahl von Beispielkonfigurationen, die GPU- und RDMA-Workloads ermöglichen, z.B. das [CNTK GPU Recipe](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI), das GPU-Treiber auf virtuellen Computern der N-Serie vorkonfiguriert und die Software des Microsoft Cognitive Toolkit als Docker-Image lädt.


## <a name="example-nvidia-gpu-drivers-on-windows-nc-vm-pool"></a>Beispiel: NVIDIA GPU-Treiber in einem Windows-NC-VM-Pool

Um CUDA-Anwendungen in einem Pool von Windows-NC-Knoten ausführen zu können, müssen Sie NVDIA GPU-Treiber installieren. In den folgenden Beispielschritten wird ein Anwendungspaket zum Installieren der NVIDIA GPU-Treiber verwendet. Sie können diese Option auswählen, wenn Ihre Workload von einer bestimmten GPU-Treiberversion abhängig ist.

1. Laden Sie ein Setuppaket für die GPU-Treiber unter Windows Server 2016 von der [NVIDIA-Website](https://www.nvidia.com/Download/index.aspx) herunter, z. B. [Version 411.82](https://us.download.nvidia.com/Windows/Quadro_Certified/411.82/411.82-tesla-desktop-winserver2016-international.exe). Speichern Sie die Datei lokal, und verwenden Sie dabei einen Kurznamen wie *GPUDriverSetup.exe*.
2. Erstellen Sie eine ZIP-Datei des Pakets.
3. Laden Sie das Paket in Ihr Batch-Konto hoch. Anweisungen finden Sie in der Anleitung zum [Anwendungspaket](batch-application-packages.md). Geben Sie eine Anwendungs-ID (z. B. *GPUDriver*) und eine Version (z. B. *411.82*) an.
1. Erstellen Sie mit den Batch-APIs oder im Azure-Portal einen Pool in der Konfiguration „Virtueller Computer“ mit der gewünschten Anzahl von Knoten und der gewünschten Skalierung. In der folgenden Tabelle werden Beispieleinstellungen für die mithilfe einer Startaufgabe im Hintergrund ausgeführte Installation der NVIDIA GPU-Treiber aufgeführt:

| Einstellung | Wert |
| ---- | ----- | 
| **Imagetyp** | Marketplace (Linux/Windows) |
| **Herausgeber** | MicrosoftWindowsServer |
| **Angebot** | Windows Server |
| **sku** | 2016-Datacenter |
| **Knotengröße** | NC6 Standard |
| **Anwendungspaketverweise** | GPUDriver, Version 411.82 |
| **Startaufgabe aktiviert** | True<br>**Befehlszeile** - `cmd /c "%AZ_BATCH_APP_PACKAGE_GPUDriver#411.82%\\GPUDriverSetup.exe /s"`<br/>**Benutzeridentität:** autouser, admin für den Pool<br/>**Erfolg abwarten:** TRUE

## <a name="example-nvidia-gpu-drivers-on-a-linux-nc-vm-pool"></a>Beispiel: NVIDIA GPU-Treiber in einem Linux-NC-VM-Pool

Um CUDA-Anwendungen in einem Pool von Linux-NC-Knoten ausführen zu können, müssen Sie die erforderlichen NVIDIA Tesla GPU-Treiber aus dem CUDA Toolkit installieren. Mit den folgenden Beispielschritten wird ein benutzerdefiniertes Ubuntu 16.04 LTS-Image mit GPU-Treibern erstellt und bereitgestellt:

1. Stellen Sie eine Azure-NC-Serien-VM mit Ubuntu 16.04 LTS bereit. Sie können den virtuellen Computer beispielsweise in der Region „USA, Süden-Mitte“ erstellen. 
2. Fügen Sie dem VM die [Erweiterung für NVIDIA GPU-Treiber](../virtual-machines/extensions/hpccompute-gpu-linux.md
) hinzu. Verwenden Sie dazu das Azure-Portal, einen mit dem Azure-Abonnement verknüpften Clientcomputer oder Azure Cloud Shell. Alternativ können Sie auch die Schritte zum Herstellen einer Verbindung mit dem virtuellen Computer und zum manuellen [Installieren der CUDA-Treiber](../virtual-machines/linux/n-series-driver-setup.md) ausführen.
3. Führen Sie die Schritte zum Erstellen einer [Momentaufnahme und eines benutzerdefinierten Linux-VM-Image](batch-custom-images.md) für Batch aus.
4. Erstellen Sie ein Batch-Konto in einer Region, die NC-VMs unterstützt.
5. Erstellen Sie mit den Batch-APIs oder dem Azure-Portal einen Pool [mit dem benutzerdefinierten Image](batch-custom-images.md) sowie der gewünschten Anzahl von Knoten und der gewünschten Skalierung. Die folgende Tabelle enthält Beispielpooleinstellungen für das Image:

| Einstellung | Wert |
| ---- | ---- |
| **Imagetyp** | Benutzerdefiniertes Image |
| **Benutzerdefiniertes Image** | *Name des Image* |
| **Knoten-Agent-SKU** | batch.node.ubuntu 16.04 |
| **Knotengröße** | NC6 Standard |

## <a name="example-microsoft-mpi-on-a-windows-h16r-vm-pool"></a>Beispiel: Microsoft MPI in einem H16r-VM-Pool unter Windows

Um Windows-MPI-Anwendungen in einem Pool von Azure H16r-VM-Knoten ausführen zu können, müssen Sie die Erweiterung „HpcVmDrivers“ konfigurieren und [Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) installieren. Nachfolgend finden Sie Beispielschritte zum Bereitstellen eines benutzerdefinierten Windows Server 2016-Image mit den erforderlichen Treibern und der erforderlichen Software:

1. Stellen Sie einen Azure H16r-VM unter Windows Server 2016 bereit. Erstellen Sie den virtuellen Computer beispielsweise in der Region „USA, Westen“. 
2. Fügen Sie dem VM die Erweiterung „HpcVmDrivers“ hinzu, indem Sie auf einem mit dem Azure-Abonnement verknüpften Clientcomputer [einen Azure PowerShell-Befehl](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances
) ausführen oder Azure Cloud Shell verwenden. 
1. Stellen Sie eine Remotedesktopverbindung mit dem virtuellen Computer her.
1. Laden Sie das [Setuppaket](https://www.microsoft.com/download/details.aspx?id=57467) („MSMpiSetup.exe“) für die neueste Version von Microsoft MPI herunter, und installieren Sie Microsoft MPI.
1. Führen Sie die Schritte zum Erstellen einer [Momentaufnahme und eines benutzerdefinierten Windows-VM-Image](batch-custom-images.md) für Batch aus.
1. Erstellen Sie mit den Batch-APIs oder dem Azure-Portal einen Pool [mit dem benutzerdefinierten Image](batch-custom-images.md) sowie der gewünschten Anzahl von Knoten und der gewünschten Skalierung. Die folgende Tabelle enthält Beispielpooleinstellungen für das Image:

| Einstellung | Wert |
| ---- | ---- |
| **Imagetyp** | Benutzerdefiniertes Image |
| **Benutzerdefiniertes Image** | *Name des Image* |
| **Knoten-Agent-SKU** | batch.node.windows amd64 |
| **Knotengröße** | H16r Standard |
| **Kommunikation zwischen Knoten aktiviert** | True |
| **Max. Aufgaben pro Knoten** | 1 |

## <a name="example-intel-mpi-on-a-linux-h16r-vm-pool"></a>Beispiel: Intel MPI in einem H16r-VM-Pool unter Linux

Eine Möglichkeit zum Ausführen von MPI-Anwendungen in einem Pool von Linux-Knoten der H-Serie besteht darin, das Image [CentOS-based 7.4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview) aus dem Azure Marketplace zu verwenden. Linux-RDMA-Treiber und Intel MPI sind vorinstalliert. Dieses Image unterstützt auch Docker-Containerworkloads.

Erstellen Sie mit den Batch-APIs oder im Azure-Portal einen Pool mit diesem Image sowie der gewünschten Anzahl von Knoten und der gewünschten Skalierung. In der folgenden Tabelle werden Beispielpooleinstellungen aufgeführt:

| Einstellung | Wert |
| ---- | ---- |
| **Imagetyp** | Marketplace (Linux/Windows) |
| **Herausgeber** | OpenLogic |
| **Angebot** | CentOS-HPC |
| **sku** | 7.4 |
| **Knotengröße** | H16r Standard |
| **Kommunikation zwischen Knoten aktiviert** | True |
| **Max. Aufgaben pro Knoten** | 1 |

## <a name="next-steps"></a>Nächste Schritte

* Beispiele zur Ausführung von MPI-Aufträgen in einem Azure Batch-Pool stehen für [Windows](batch-mpi.md) und [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) zur Verfügung.

* Beispiele für GPU-Workloads in Batch finden Sie in [Batch Shipyard](https://github.com/Azure/batch-shipyard/).