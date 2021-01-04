---
title: include file
description: include file
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: caad292f06710fca8d6f64476eead5dfcb164e9d
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96536923"
---
## <a name="supported-operating-systems-and-drivers"></a>Unterstützte Betriebssysteme und Treiber

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla-Treiber (CUDA)

NVIDIA Tesla-Treiber (CUDA) für VMs der Serien NC, NCv2, NCv3, NCasT4_v3, ND und NDv2 (optional für NV-Serie) werden nur unter den in der folgenden Tabelle aufgeführten Betriebssystemen unterstützt. Die Links zum Herunterladen der Treiber sind zum Zeitpunkt der Veröffentlichung aktuell. Die neuesten Treiber finden Sie auf der [NVIDIA](https://www.nvidia.com/)-Website.

> [!TIP]
> Alternativ zur manuellen CUDA-Treiberinstallation auf einem virtuellen Windows Server-Computer können Sie ein [Azure Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md)-Image bereitstellen. Bei den DSVM-Editionen für Windows Server 2016 sind NVIDIA CUDA-Treiber, die CUDA Deep Neural Network-Bibliothek und weitere Tools vorinstalliert.


| OS | Treiber |
| -------- |------------- |
| Windows Server 2019 | [451.82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (EXE-Datei) |
| Windows Server 2016 | [451.82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (EXE-Datei) |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID-Treiber

Microsoft vertreibt Installationsprogramme für NVIDIA GRID-Treiber für virtuelle Computer der NV- und NVv3-Serie, die als virtuelle Arbeitsstationen oder für virtuelle Anwendungen verwendet werden. Installieren Sie nur diese GRID-Treiber auf virtuellen Azure-Computern der NV-Serie, und zwar nur unter den in der folgenden Tabelle aufgeführten Betriebssystemen. In diesen Treibern ist die Lizenzierung für virtuelle GRID-GPU-Software in Azure enthalten. Sie müssen keinen NVIDIA vGPU-Softwarelizenzserver einrichten.

Die von Azure neu verteilten GRID-Treiber funktionieren nicht auf Nicht-NV-Serien-VMs wie NC, NCv2, NCv3, ND sowie NDv2-Serien-VMs. Die einzige Ausnahme ist die VM-Serie „NCas_T4_V3“, bei der die GRID-Treiber die Grafikfunktionen ähnlich wie bei der NV-Serie aktivieren.

Beachten Sie, dass durch die NVIDIA-Erweiterung immer der neueste Treiber installiert wird. Für Kunden, die eine ältere Version benötigen, stellen wir hier Links zur Vorgängerversion bereit.

Für Windows Server 2019, Windows Server 2016 und Windows 10 (bis Build 2004):
- [GRID 11.2 (452.57)](https://go.microsoft.com/fwlink/?linkid=874181) (EXE-Datei)
- [GRID 11.1 (452.39)](https://download.microsoft.com/download/9/9/1/99186e1b-d27d-47d5-9957-175c88f4efbe/452.39_grid_win10_64bit_whql.exe) (EXE-Datei) 

Für Windows Server 2012 R2: 
- [GRID 11.0 (451.48)](https://download.microsoft.com/download/f/7/2/f729e28b-57b8-4141-b577-38d2390973ef/451.48_grid_server2012R2_64bit_international.exe) (EXE-Datei) 
- [GRID 10.1 (442.66)](https://download.microsoft.com/download/4/3/3/4330fd5c-c685-4ca1-abca-3b2fb3c11d2e/442.06_grid_win8_win7_64bit_international_whql.exe) (EXE-Datei)  

Eine vollständige Liste aller vorherigen NVIDIA GRID-Treiber finden Sie auf [GitHub](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json).
