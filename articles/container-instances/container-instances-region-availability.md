---
title: Ressourcenverfügbarkeit für Azure Container Instances
description: Verfügbarkeit von Compute- und Arbeitsspeicherressourcen für den Azure Container Instances-Dienst in verschiedenen Azure-Regionen
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 05/14/2019
ms.author: danlep
ms.openlocfilehash: 64b60178413e470cc7fe9b3991c6fc29b5a0f860
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794292"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Ressourcenverfügbarkeit für Azure Container Instances in Azure-Regionen

In diesem Artikel wird die Verfügbarkeit der Compute- und Arbeitsspeicherressourcen von Azure Container Instances in Azure-Regionen erläutert. 

Die angezeigten Werte stellen die maximalen Ressourcen dar, die pro Bereitstellung einer [Containergruppe](container-instances-container-groups.md) zur Verfügung stehen. Die Werte sind zum Zeitpunkt der Veröffentlichung aktuell. 

> [!NOTE]
> Containergruppen, die innerhalb dieser Ressourcenlimits erstellt werden, unterliegen der Verfügbarkeit in der Bereitstellungsregion. Wenn eine Region stark ausgelastet ist, kann bei der Bereitstellung von Instanzen ein Fehler auftreten. Um einen solchen Fehler bei der Bereitstellung zu beheben, versuchen Sie, Instanzen mit niedrigeren Ressourceneinstellungen bereitzustellen, oder führen Sie die Bereitstellung zu einem späteren Zeitpunkt durch.

Weitere Informationen zu Kontingenten und anderen Grenzwerten in Ihren Bereitstellungen finden Sie unter [Kontingente und Regionsverfügbarkeit für Azure Container Instances](container-instances-quotas.md).

## <a name="availability---general"></a>Verfügbarkeit: allgemein

Die folgenden Regionen und Ressourcen stehen Containergruppen mit Linux- und [unterstützten](container-instances-faq.md#what-windows-base-os-images-are-supported) Windows Server 2016-basierten Containern zur Verfügung.

| Location | Betriebssystem | CPU | Arbeitsspeicher (GB) |
| -------- | -- | :---: | :-----------: |
| „Kanada, Mitte“, „Indien, Mitte“, „USA, Mitte“, „Asien, Osten“, „USA, Osten“, „USA, Osten 2“, „Europa, Norden“, „USA, Süden-Mitte“, „Asien, Südosten“, „Vereinigtes Königreich, Süden“, „USA, Westen“ | Linux | 4 | 16 |
| „Europa, Westen“, „USA, Westen 2“ | Linux | 4 | 14 |
| „Australien, Osten“, „Japan, Osten“ | Linux | 2 | 8 |
| „USA, Norden-Mitte“; „Indien, Süden“ | Linux | 2 | 3,5 |
| Europa, Westen | Windows | 4 | 16 |
| USA, Osten; USA, Westen | Windows | 4 | 14 |
| „Australien, Osten“, „Kanada, Mitte“, „Indien, Mitte“, „USA, Mitte“, „Asien, Osten“, „USA, Osten 2“, „Japan, Osten“, „USA, Norden-Mitte“, „Europa, Norden“, „USA, Süden-Mitte“, „Asien, Südosten“, „Indien, Süden“, „Vereinigtes Königreich, Süden“, „USA, Westen 2“ | Windows | 2 | 3,5 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Verfügbarkeit – Windows Server 2019 LTSC, 1809 Bereitstellungen (Vorschauversion)

Die folgenden Regionen und Ressourcen stehen Containergruppen mit Windows Server 2019-basierten Containern zur Verfügung (Vorschauversion).

| Location | Betriebssystem | CPU | Arbeitsspeicher (GB) |
| -------- | -- | :---: | :-----------: |
| „Asien, Südosten“, „Europa, Norden“, „Europa, Westen“, „USA, Mitte“, „USA, Osten“, „USA, Westen“, „USA, Westen 2“ | Windows | 4 | 16 |
| USA (Ost) 2 | Windows | 2 | 3,5 |


## <a name="availability---virtual-network-deployment-preview"></a>Verfügbarkeit: Bereitstellung eines virtuellen Netzwerks (Vorschauversion)

Für eine Containergruppe, die in einem [virtuellen Azure-Netzwerk](container-instances-vnet.md) (Vorschau) bereitgestellt wird, sind die folgenden Regionen und Ressourcen verfügbar.

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Verfügbarkeit: GPU-Ressourcen (Vorschauversion)

Für eine Containergruppe, die mit [GPU-Ressourcen](container-instances-gpu.md) (Vorschau) bereitgestellt wird, sind die folgenden Regionen und Ressourcen verfügbar.

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Nächste Schritte

Teilen Sie dem Team auf [aka.ms/aci/feedback](https://aka.ms/aci/feedback) mit, wenn zusätzliche Regionen und eine Erhöhung der Ressourcenverfügbarkeit erforderlich sind.

Informationen zur Problembehandlung bei der Bereitstellung von Containerinstanzen finden Sie unter [Beheben von Bereitstellungsproblemen für Azure Container Instances](container-instances-troubleshooting.md).
