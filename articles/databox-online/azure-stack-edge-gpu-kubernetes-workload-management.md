---
title: Grundlegendes zur Kubernetes-Workloadverwaltung auf einem Azure Stack Edge Pro-Gerät | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Kubernetes-Workloads auf Ihrem Azure Stack Edge Pro-Gerät verwaltet werden können.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: ef840b3d9db4e82eeecea37079a08ccb0858a77b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448529"
---
# <a name="kubernetes-workload-management-on-your-azure-stack-edge-pro-device"></a>Kubernetes-Workloadverwaltung auf Ihrem Azure Stack Edge Pro-Gerät

Wenn Sie die Computerolle konfigurieren, wird auf Ihrem Azure Stack Edge Pro-Gerät ein Kubernetes-Cluster erstellt. Sobald der Kubernetes-Cluster erstellt wurde, können innerhalb des Kubernetes-Clusters Containeranwendungen in Pods bereitgestellt werden. Es gibt verschiedene Möglichkeiten, Workloads in Ihrem Kubernetes-Cluster bereitzustellen. 

In diesem Artikel werden die verschiedenen Methoden beschrieben, die zum Bereitstellen von Workloads auf dem Azure Stack Edge Pro-Gerät verwendet werden können.

## <a name="workload-types"></a>Workloadtypen

Die beiden gängigen Workloadtypen, die Sie auf Ihrem Azure Stack Edge Pro-Gerät bereitstellen können, sind zustandslose oder zustandsbehaftete Anwendungen.

- **Zustandslose Anwendungen** behalten ihren Zustand nicht bei und speichern keine Daten im beständigen Speicher. Alle Benutzer- und Sitzungsdaten bleiben im Client. Einige Beispiele für zustandslose Anwendungen sind Web-Front-Ends wie Nginx und andere Webanwendungen.

    Sie können eine Kubernetes-Bereitstellung erstellen, um eine zustandslose Anwendung in Ihrem Cluster bereitzustellen. 

- **Zustandsbehaftete Anwendungen** erfordern, dass ihr Zustand gespeichert wird. Zustandsbehaftete Anwendungen verwenden beständigen Speicher, z. B. beständige Volumes, um Daten zur Verwendung durch den Server oder andere Benutzer zu speichern. Beispiele für zustandsbehaftete Anwendungen sind Datenbanken wie [Azure SQL Edge](../azure-sql-edge/overview.md) und MongoDB.

    Sie können eine Kubernetes-Bereitstellung erstellen, um eine zustandsbehaftete Anwendung bereitzustellen. 

## <a name="deployment-flow"></a>Bereitstellungsablauf

Zum Bereitstellen von Anwendungen auf einem Azure Stack Edge Pro-Gerät führen Sie die folgenden Schritte aus: 
 
1. **Konfigurieren des Zugriffs**: Zuerst verwenden Sie den PowerShell-Runspace, um einen Benutzer zu erstellen, einen Namespace zu erstellen und dem Benutzer Zugriff auf diesen Namespace zu gewähren.
2. **Konfigurieren des Speichers**: Als nächstes verwenden Sie die Azure Stack Edge-Ressource im Azure-Portal, um beständige Volumes entweder mithilfe der statischen oder dynamischen Bereitstellung für die zustandsbehafteten Anwendungen zu erstellen, die Sie bereitstellen werden.
3. **Konfigurieren des Netzwerks**: Schließlich verwenden Sie die Dienste, um Anwendungen extern und innerhalb des Kubernetes-Clusters verfügbar zu machen.
 
## <a name="deployment-types"></a>Bereitstellungstypen

Es gibt drei Hauptmethoden zum Bereitstellen von Workloads. Mit jeder dieser Bereitstellungsmethoden können Sie eine Verbindung mit einem eindeutigen Namespace auf dem Gerät herstellen und dann zustandslose oder zustandsbehaftete Anwendungen bereitstellen.

![Kubernetes-Workloadbereitstellung](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **Lokale Bereitstellung**: Diese Bereitstellung erfolgt über das Befehlszeilenzugriffs-Tool, wie z. B. `kubectl`, mit dem Sie Kubernetes-`yamls` bereitstellen können. Auf Ihrem Azure Stack Edge Pro-Gerät greifen Sie über die Datei `kubeconfig` auf den Kubernetes-Cluster zu. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit einem Kubernetes-Cluster und Verwalten des Clusters mit kubectl auf Ihrem Azure Stack Edge-GPU-Gerät](azure-stack-edge-gpu-create-kubernetes-cluster.md).

- **IoT Edge-Bereitstellung**: Dies erfolgt über IoT Edge, worüber eine Verbindung mit Azure IoT Hub hergestellt wird. Auf Ihrem Azure Stack Edge Pro-Gerät stellen Sie über den `iotedge`-Namespace eine Verbindung mit dem Kubernetes-Cluster her. Die in diesem Namespace bereitgestellten IoT Edge-Agents sind für die Konnektivität mit Azure verantwortlich. Sie wenden die `IoT Edge deployment.json`-Konfiguration mithilfe von Azure DevOps CI/CD an. Die Namespace- und IoT Edge-Verwaltung erfolgt über den Cloudoperator.

- **Kubernetes-Bereitstellung mit Azure Arc-Unterstützung**: Kubernetes mit Azure Arc-Unterstützung ist ein Hybridverwaltungstool, mit dem Sie Anwendungen in Ihren Kubernetes-Clustern bereitstellen können. Auf Ihrem Azure Stack Edge Pro-Gerät stellen Sie über den `azure-arc namespace` eine Verbindung mit dem Kubernetes-Cluster her. Die in diesem Namespace bereitgestellten Agents sind für die Konnektivität mit Azure verantwortlich. Sie wenden die Bereitstellungskonfiguration mithilfe der GitOps-basierten Konfigurationsverwaltung an. 
    
    Kubernetes mit Azure Arc-Unterstützung ermöglicht außerdem die Verwendung von Azure Monitor für Container, um Ihre Cluster anzuzeigen und zu überwachen. Weitere Informationen finden Sie unter [Was ist Kubernetes mit Azure Arc-Unterstützung?](../azure-arc/kubernetes/overview.md).

## <a name="choose-the-deployment-type"></a>Auswählen des Bereitstellungstyps

Beachten Sie beim Bereitstellen von Anwendungen die folgenden Informationen:

- **Einzelner oder mehrere Typen**: Sie können eine einzelne Bereitstellungsoption oder eine Mischung verschiedener Bereitstellungsoptionen auswählen.
- **Cloud oder lokal**: Abhängig von Ihren Anwendungen können Sie die lokale Bereitstellung über kubectl oder die Cloudbereitstellung über IoT Edge und Azure Arc auswählen. 
    - Wenn Sie eine lokale Bereitstellung auswählen, sind Sie auf das Netzwerk beschränkt, in dem Ihr Azure Stack Edge Pro-Gerät bereitgestellt wird.
    - Wenn Sie über einen Cloud-Agent verfügen, den Sie bereitstellen können, sollten Sie den Cloudoperator bereitstellen und die Cloudverwaltung verwenden.
- **IoT oder Azure Arc**: Die Auswahl der Bereitstellung hängt auch von der Absicht ab, die Sie mit Ihrem Produktszenario verbinden. Wenn Sie Anwendungen oder Container bereitstellen, die tiefer in IoT oder das IoT-Ökosystem integriert sind, wählen Sie IoT Edge aus, um Ihre Anwendungen bereitzustellen. Wenn Sie bereits über Kubernetes-Bereitstellungen verfügen, ist Azure Arc die bevorzugte Wahl.


## <a name="next-steps"></a>Nächste Schritte

Informationen zum lokalen Bereitstellen einer App über kubectl finden Sie unter:

- [Bereitstellen einer zustandslosen Anwendung auf einem Azure Stack Edge Pro-Gerät über kubectl](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).

Informationen zum Bereitstellen einer App über IoT Edge finden Sie unter:

- [Bereitstellen eines Beispielmoduls auf Ihrem Azure Stack Edge Pro-Gerät über IoT Edge](azure-stack-edge-gpu-deploy-sample-module.md).

Informationen zum Bereitstellen einer App über Azure Arc finden Sie unter:

- [Aktivieren von Azure Arc auf einem Kubernetes-Cluster auf Ihrem Azure Stack Edge-GPU-Gerät](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).