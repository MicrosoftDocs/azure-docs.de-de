---
title: Größen von virtuellen Azure Windows-Computern – GPU | Microsoft-Dokumentation
description: Auflistung der verschiedenen verfügbaren GPU-optimierten Größen für virtuelle Windows-Computer in Azure. Dieser Artikel listet Informationen zur Anzahl von vCPUs, Datenträgern und Netzwerkschnittstellenkarten sowie zum Speicherdurchsatz und zur Netzwerkbandbreite für Größen dieser Serie auf.
services: virtual-machines-windows
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/11/2019
ms.author: jonbeck
ms.openlocfilehash: 57a19b11d7aead6f67f411b75b046a9238cf7c12
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055232"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Für GPU optimierte VM-Größen

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Unterstützte Betriebssysteme und Treiber

Um die GPU-Funktionen von virtuellen Azure-Computern der N-Serie unter Windows nutzen zu können, müssen NVIDIA-GPU-Treiber installiert werden. Mit der [NVIDIA-GPU-Treibererweiterung](../extensions/hpccompute-gpu-windows.md) werden entsprechende NVIDIA-CUDA- oder GRID-Treiber auf einem virtuellen Computer der N-Serie installiert. Installieren oder verwalten Sie die Erweiterung mithilfe des Azure-Portals oder mit Tools wie Azure PowerShell oder Azure Resource Manager-Vorlagen. Informationen zu unterstützten Betriebssystemen und Bereitstellungsschritten finden Sie in der [Dokumentation zur NVIDIA-GPU-Treibererweiterung](../extensions/hpccompute-gpu-windows.md). Allgemeine Informationen zu VM-Erweiterungen finden Sie unter [Erweiterungen und Features für virtuelle Azure-Computer](../extensions/overview.md).

Wenn Sie NVIDIA-GPU-Treiber manuell installieren möchten, finden Sie Informationen zu unterstützten Betriebssystemen und Treibern sowie Schritte zur Installation und Überprüfung unter [Einrichten von GPU-Treibern der N-Serie für Windows](n-series-driver-setup.md).

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

## <a name="other-sizes"></a>Andere Größen
- [Allgemeiner Zweck](sizes-general.md)
- [Computeoptimiert](sizes-compute.md)
- [High Performance Computing](sizes-hpc.md)
- [Arbeitsspeicheroptimiert](sizes-memory.md)
- [Speicheroptimiert](sizes-storage.md)
- [Vorherige Generationen](sizes-previous-gen.md)

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen dazu, wie Sie mit [Azure-Computeeinheiten (ACU)](acu.md) die Computeleistung von Azure-SKUs vergleichen können.

