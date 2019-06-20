---
title: Größen von virtuellen Linux-Computern in Azure | Microsoft-Dokumentation
description: Listet die verschiedenen verfügbaren Größen für virtuelle Linux-Computer in Azure auf.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: da681171-f045-4c80-a5a9-d8bd47964673
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/14/2018
ms.author: jonbeck
ms.openlocfilehash: c87a192051e34bebf6c1b137c0e96f755a0bb492
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873244"
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Größen für virtuelle Linux-Computer in Azure
Dieser Artikel beschreibt die verfügbaren Größen und Optionen für die virtuellen Azure-Computer, die Sie zum Ausführen Ihrer Linux-Apps und Workloads verwenden können. Darüber hinaus werden Überlegungen zur Bereitstellung angestellt, die Sie berücksichtigen sollten, wenn Sie eine Verwendung dieser Ressourcen planen. Dieser Artikel ist auch für [virtuelle Windows-Computer](../windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)verfügbar.


| Type                     | Größen           |    BESCHREIBUNG       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Allgemeiner Zweck](sizes-general.md)          | B, Dsv3, Dv3, DSv2, Dv2, Av2, DC  | Ausgewogenes Verhältnis von CPU zu Arbeitsspeicher. Ideal für Tests und Entwicklung, kleine bis mittlere Datenbanken sowie Webserver mit geringer bis mittlerer Auslastung. |
| [Computeoptimiert](sizes-compute.md)        | Fsv2           | Hohes Verhältnis von CPU zu Arbeitsspeicher. Ideal für Webserver, Network Appliances, Stapelverarbeitungsvorgänge und Anwendungsserver mit mittlerer Auslastung.        |
| [Arbeitsspeicheroptimiert](sizes-memory.md)         | Esv3, Ev3, M, GS, G, DSv2, Dv2  | Hohes Verhältnis von Speicher zu CPU. Hervorragend geeignet für relationale Datenbankserver, mittlere bis große Caches und In-Memory-Analysen.                 |
| [Speicheroptimiert](sizes-storage.md)        | Lsv2, Ls                | Hoher Datenträgerdurchsatz und E/A, ideal für Big Data, SQL, NoSQL-Datenbanken, Datawarehousing und große transaktionale Datenbanken.  |
| [GPU](sizes-gpu.md)            | NV, NVv2, NC, NCv2, NCv3, ND, NDv2 (Vorschauversion)            | Spezielle virtuelle Computer als Ziel für aufwendiges Grafikrendering und Videobearbeitung sowie für Modelltraining und Rückschlüsse (ND) mit Deep Learning. Mit einem oder mehreren GPUs verfügbar.       |
| [High Performance Computing](sizes-hpc.md) | H       | Unsere virtuellen Computer mit den schnellsten und leistungsfähigsten CPUs, die optional über Netzwerkschnittstellen mit hohem Durchsatz (RDMA) verfügen. |


<br>

- Informationen zu den Preisen der unterschiedlichen Größen finden Sie unter [Virtual Machines – Preise](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux). 
- Informationen zur Verfügbarkeit von VM-Größen in Azure-Regionen finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/regions/services/).
- Informationen zu den allgemeinen Einschränkungen von virtuellen Azure-Computern finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../../azure-subscription-service-limits.md).
- Weitere Informationen dazu, wie Sie mit [Azure-Computeeinheiten (ACU)](acu.md) die Computeleistung von Azure-SKUs vergleichen können.


## <a name="rest-api"></a>REST-API

Informationen zur Verwendung der REST-API, um VM-Größen abzufragen, finden Sie nachstehend:

- [List available virtual machine sizes for resizing (Auflisten der verfügbaren Größen virtueller Computer zur Größenänderung)](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [List available virtual machine sizes for a subscription (Auflisten der verfügbaren Größen virtueller Computer für ein Abonnement)](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [List available virtual machine sizes in an availability set (Auflisten der verfügbaren Größen virtueller Computer in einer Verfügbarkeitsgruppe)](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Weitere Informationen dazu, wie Sie mit [Azure-Computeeinheiten (ACU)](acu.md) die Computeleistung von Azure-SKUs vergleichen können.

## <a name="benchmark-scores"></a>Benchmarkergebnisse

Weitere Informationen zur Computeleistung für virtuelle Linux-Computer unter Verwendung der [CoreMark-Benchmarkergebnisse](compute-benchmark-scores.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den verschiedenen verfügbaren Größen von virtuellen Computern:
- [Allgemeiner Zweck](sizes-general.md)
- [Computeoptimiert](sizes-compute.md)
- [Arbeitsspeicheroptimiert](sizes-memory.md)
- [Speicheroptimiert](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [High Performance Computing](sizes-hpc.md)
- Auf der Seite [Vorherige Generation](sizes-previous-gen.md) finden Sie die Serien A Standard, Dv1 (D1–4 und D11–14 v1) sowie A8–A11.



