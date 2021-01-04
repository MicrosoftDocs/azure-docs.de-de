---
title: Konfigurieren der GPU-Überwachung mit Azure Monitor für Container | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie die Überwachung von Kubernetes-Clustern mit NVIDIA- und AMD-GPU-fähigen Knoten mit Azure Monitor für Container konfigurieren können.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: e391117ab57211aa5d178d11c27b934b4ccd37f8
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905586"
---
# <a name="configure-gpu-monitoring-with-azure-monitor-for-containers"></a>Konfigurieren der GPU-Überwachung mit Azure Monitor für Container

Ab der Agent-Version *ciprod03022019* unterstützt der integrierte Agent von Azure Monitor für Container das Überwachen der GPU-Nutzung (Graphical Processing Unit) auf GPU-fähigen Kubernetes-Clusterknoten sowie das Überwachen von Pods/Containern, die GPU-Ressourcen anfordern und verwenden.

## <a name="supported-gpu-vendors"></a>Unterstützte GPU-Anbieter

Azure Monitor für Container unterstützt die Überwachung von GPU-Clustern von folgenden GPU-Anbietern:

- [NVIDIA](https://developer.nvidia.com/kubernetes-gpu)

- [AMD](https://github.com/RadeonOpenCompute/k8s-device-plugin)

Azure Monitor für Container beginnt automatisch mit der Überwachung der GPU-Nutzung auf Knoten sowie GPUs anfordernden Pods und Workloads, indem die folgenden Metriken in 60-Sekunden-Intervallen gesammelt und in der **insightMetrics**-Tabelle gespeichert werden.

>[!NOTE]
>Stellen Sie nach dem Bereitstellen eines Clusters mit GPU-Knoten sicher, dass der [GPU-Treiber](../../aks/gpu-cluster.md) installiert ist, wie von AKS für die Ausführung von GPU-Workloads erforderlich. Azure Monitor für Container sammelt GPU-Metriken über GPU-Treiberpods, die auf dem Knoten ausgeführt werden. 

|Metrikname |Metrikdimension (Tags) |Beschreibung |
|------------|------------------------|------------|
|containerGpuDutyCycle |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor|Der Prozentsatz der Zeit im Verlauf des letzten Beispielzeitraums (60 Sekunden), während dessen die GPU ausgelastet war/aktiv die Verarbeitung für einen Container ausgeführt hat. Der Arbeitszyklus ist eine Zahl zwischen 1 und 100. |
|containerGpuLimits |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |In jedem Container können Grenzwerte als eine oder mehrere GPUs angegeben werden. Es ist nicht möglich, einen Bruchteil einer GPU anzufordern oder einzuschränken. |
|containerGpuRequests |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |Jeder Container kann einen oder mehrere GPUs anfordern. Es ist nicht möglich, einen Bruchteil einer GPU anzufordern oder einzuschränken.|
|containerGpumemoryTotalBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Menge an GPU-Arbeitsspeicher in Bytes, die für einen bestimmten Container verwendet werden kann. |
|containerGpumemoryUsedBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Menge an GPU-Arbeitsspeicher in Bytes, die für einen bestimmten Container verwendet wird. |
|nodeGpuAllocatable |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Anzahl von GPUs in einem Knoten, die von Kubernetes verwendet werden können. |
|nodeGpuCapacity |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Gesamtanzahl der GPUs in einem Knoten. |

## <a name="gpu-performance-charts"></a>GPU-Leistungsdiagramme 

Azure Monitor für Container enthält vorkonfigurierte Diagramme für die Metriken, die weiter oben in der Tabelle als GPU-Arbeitsmappe für jeden Cluster aufgeführt sind. Eine Beschreibung der für Azure Monitor für Container verfügbaren Arbeitsmappen finden Sie unter [Arbeitsmappen in Azure Monitor für Container](container-insights-reports.md).

## <a name="next-steps"></a>Nächste Schritte

- In [Verwenden von GPUs für computeintensive Workloads in Azure Kubernetes Service (AKS)](../../aks/gpu-cluster.md) erfahren Sie, wie Sie einen AKS-Cluster bereitstellen, der GPU-fähige Knoten enthält.

- Erfahren Sie mehr über [Für GPU optimierte VM-Größen](../../virtual-machines/sizes-gpu.md).

- In [Schedule GPUs](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) (Planen von GPUs)erfahren Sie mehr darüber, wie Kubernetes Experimente zur Verwaltung von GPUs auf einem oder mehreren Knoten in einem Cluster unterstützt.
