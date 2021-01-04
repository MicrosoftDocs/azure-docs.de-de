---
title: Verwenden der GPU- oder VPU-Computebeschleunigung auf Azure Stack Edge-Geräten in Kubernetes-Bereitstellungen | Microsoft-Dokumentation
description: Hier wird beschrieben, wie die GPU- oder VPU-Computebeschleunigung in Kubernetes-Bereitstellungen für Geräte vom Typ „Azure Stack Edge Pro GPU“, „Azure Stack Edge Pro R“ oder „Azure Stack Edge Mini Ri“ verwendet wird.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/05/2020
ms.author: alkohli
ms.openlocfilehash: cf70b24dae70ad2e64f3443e4c4d959d46fb4ea4
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095031"
---
# <a name="use-compute-acceleration-on-azure-stack-edge-pro-gpu-for-kubernetes-deployment"></a>Verwenden der Computebeschleunigung für Azure Stack Edge Pro GPU in Kubernetes-Bereitstellungen

In diesem Artikel wird beschrieben, wie Sie die Computebeschleunigung für Azure Stack Edge-Geräte in Kubernetes-Bereitstellungen verwenden. Die Informationen in diesem Artikel beziehen sich auf Geräte vom Typ „Azure Stack Edge Pro GPU“, „Azure Stack Edge Pro R“ und „Azure Stack Edge Mini R“.


## <a name="about-compute-acceleration"></a>Informationen zur Computebeschleunigung 

Der Zentralprozessor (Central Processing Unit, CPU) ist die universelle Standard-Computingeinheit für die meisten Prozesse, die auf einem Computer ausgeführt werden. Häufig wird eine spezialisierte Computerhardware verwendet, um einige Funktionen effizienter auszuführen als die, die in der Software einer CPU ausgeführt werden. Ein Grafikprozessor (Graphics Processing Unit, GPU) kann z. B. verwendet werden, um die Verarbeitung von Pixeldaten zu beschleunigen.  

Computebeschleunigung ist ein Begriff, der insbesondere für Azure Stack Edge-Geräte verwendet wird, bei denen ein Grafikprozessor (GPU), eine Vision Processing Unit (VPU) oder ein Field Programmable Gate Array (FPGA) zur Hardwarebeschleunigung eingesetzt werden. Für die meisten Workloads, die auf einem Azure Stack Edge-Gerät bereitgestellt werden, ist das Timing von entscheidender Bedeutung, und es werden mehrere Kamerastreams und/oder hohe Bildfrequenzen verwendet. All dies erfordert eine besondere Hardwarebeschleunigung.

In diesem Artikel wird nur die GPU- oder VPU-Computebeschleunigung für die folgenden Geräte erörtert:

- **Azure Stack Edge Pro GPU**: Diese Geräte unterstützen 1 oder 2 Nvidia T4 Tensor Core GPUs. Weitere Informationen finden Sie unter [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/).
- **Azure Stack Edge Pro R**: Diese Geräte unterstützen 1 Nvidia T4 Tensor Core GPU. Weitere Informationen finden Sie unter [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/).
- **Azure Stack Edge Mini R**: Diese Geräte unterstützen 1 Intel Movidius Myriad X VPU. Weitere Informationen finden Sie unter [Intel Movidius Myriad X VPU](https://www.movidius.com/MyriadX).


## <a name="use-gpu-for-kubernetes-deployment"></a>Verwenden von GPU für Kubernetes-Bereitstellungen

Die folgende Beispiel-`yaml` kann für ein Gerät vom Typ „Azure Stack Edge Pro GPU“ oder „Azure Stack Edge Pro R“ mit einer GPU verwendet werden.

<!--In a production scenario, Pods are not used directly and these are wrapped around higher level constructs like Deployment, ReplicaSet which maintain the desired state in case of pod restarts, failures.-->

```yml
apiVersion: v1
kind: Pod
metadata:
  name: gpu-pod
spec:
  containers:
    - name: cuda-container
      image: nvidia/cuda:9.0-devel
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
    - name: digits-container
      image: nvidia/digits:6.0
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
```


## <a name="use-vpu-for-kubernetes-deployment"></a>Verwenden von VPU für Kubernetes-Bereitstellungen

Die folgende Beispiel-`yaml` kann für ein Gerät vom Typ „Azure Stack Edge Mini R“ verwendet werden, das mit einer VPU ausgestattet ist.

```yml
apiVersion: batch/v1
kind: Job
metadata:
 name: intelvpu-demo-job
 labels:
   jobgroup: intelvpu-demo
spec:
 template:
   metadata:
     labels:
       jobgroup: intelvpu-demo
   spec:
     restartPolicy: Never
     containers:
       -
         name: intelvpu-demo-job-1
         image: ubuntu-demo-openvino:devel
         imagePullPolicy: IfNotPresent
         command: [ "/do_classification.sh" ]
         resources:
           limits:
             vpu.intel.com/hddl: 1
```


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über [Verwenden von kubectl zum Ausführen einer zustandsbehafteten Kubernetes-Anwendung mit PersistentVolume auf einem Azure Stack Edge Pro-GPU-Gerät](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md).
