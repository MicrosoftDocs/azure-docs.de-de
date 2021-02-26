---
title: Containersicherheit in Azure Security Center | Microsoft-Dokumentation
description: Hier erfahren Sie mehr über die Containersicherheitsfeatures von Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/07/2021
ms.author: memildin
ms.openlocfilehash: eb70a31d0fa5f231bd0db8ca27517ce43fe1db28
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007811"
---
# <a name="container-security-in-security-center"></a>Containersicherheit in Security Center

Azure Security Center ist die native Azure-Lösung zum Sichern Ihrer Container.

Security Center kann die folgenden Containerressourcentypen schützen:

| Ressourcentyp | Schutzmaßnahmen von Security Center |
|:--------------------:|-----------|
| ![Kubernetes-Dienst](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)<br>**AKS-Cluster (Azure Kubernetes Service)** | - Kontinuierliche Bewertung der Konfigurationen Ihrer AKS-Cluster, um Einblicke in Fehlkonfigurationen bereitzustellen, sowie Anleitungen, die Ihnen helfen, die erkannten Probleme zu beheben.<br>[Erfahren Sie mehr über die Umgebungshärtung durch Sicherheitsempfehlungen.](#environment-hardening)<br><br>- Bedrohungsschutz für AKS-Cluster und Linux-Knoten. Warnungen zu verdächtigen Aktivitäten werden vom optionalen [Azure Defender für Kubernetes](defender-for-kubernetes-introduction.md) bereitgestellt.<br>[Erfahren Sie mehr über den Laufzeitschutz für AKS-Knoten und -Cluster.](#run-time-protection-for-aks-nodes-and-clusters)|
| ![Containerhost](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)<br>**Containerhosts**<br>(VMs, auf denen Docker ausgeführt wird) | - Kontinuierliche Bewertung der Docker-Konfigurationen, um Einblicke in Fehlkonfigurationen bereitzustellen, sowie Anleitungen, die Ihnen helfen, die erkannten Probleme mit dem optionalen [Azure Defender für Server](defender-for-servers-introduction.md) zu beheben.<br>[Erfahren Sie mehr über die Umgebungshärtung durch Sicherheitsempfehlungen.](#environment-hardening)|
| ![Containerregistrierung](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)<br>**Azure Container Registry-Registrierungen (ACR)** | - Tools zur Sicherheitsbewertung und -verwaltung für die Images in Ihren Azure Resource Manager-basierten ACR-Registrierungen mit dem optionalen [Azure Defender für Containerregistrierungen](defender-for-container-registries-introduction.md).<br>[Erfahren Sie mehr über das Scannen von Containerimages auf Sicherheitsrisiken.](#vulnerability-management---scanning-container-images) |
|||

In diesem Artikel wird beschrieben, wie Sie Security Center mit den optionalen Azure Defender-Plänen für Containerregistrierungen, Server und Kubernetes verwenden, um die Sicherheit Ihrer Container und Ihrer Apps zu verbessern, zu überwachen und zu verwalten.

Sie erfahren, wie Security Center diese Kernaspekte der Containersicherheit unterstützt:

- [Verwaltung von Sicherheitsrisiken: Scannen von Containerimages](#vulnerability-management---scanning-container-images)
- [Umgebungshärtung](#environment-hardening)
- [Laufzeitschutz für AKS-Knoten und -Cluster](#run-time-protection-for-aks-nodes-and-clusters)

Der folgende Screenshot zeigt die Seite mit dem Ressourcenbestand (Asset Inventory) und die verschiedenen Containerressourcentypen, die mit Security Center geschützt werden.

:::image type="content" source="./media/container-security/container-security-tab.png" alt-text="Containerbezogene Ressourcen auf der Seite mit dem Ressourcenbestand von Security Center" lightbox="./media/container-security/container-security-tab.png":::

## <a name="vulnerability-management---scanning-container-images"></a>Verwaltung von Sicherheitsrisiken: Scannen von Containerimages

Wenn Sie Images in Ihren Azure Resource Manager-basierten Azure-Containerregistrierungen überwachen möchten, aktivieren Sie [Azure Defender für Containerregistrierungen](defender-for-container-registries-introduction.md). Security Center scannt alle Images, die gepusht, importiert oder in den letzten 30 Tagen gepullt wurden. Der integrierte Scanner wird von Qualys bereitgestellt. Hierbei handelt es sich um einen branchenführenden Anbieter von Tools zur Überprüfung auf Sicherheitsrisiken.

Werden Probleme gefunden – von Qualys oder Security Center –, werden Sie auf dem [Azure Defender-Dashboard](azure-defender-dashboard.md) benachrichtigt. Für jedes Sicherheitsrisiko bietet Security Center Handlungsempfehlungen sowie eine Klassifizierung des Schweregrads und Anleitungen für die Behebung des Problems. Ausführliche Informationen zu Security Center-Empfehlungen für Container finden Sie in der [Referenzliste der Empfehlungen](recommendations-reference.md#recs-compute).

Security Center filtert und klassifiziert die Ergebnisse des Scanners. Wenn ein Image fehlerfrei ist, markiert Security Center es entsprechend. Security Center generiert Sicherheitsempfehlungen nur für Images, bei denen Probleme behoben werden müssen. Indem Sie nur benachrichtigt werden, wenn Probleme auftreten, reduziert Security Center das Potenzial von unerwünschten Informationswarnungen.

## <a name="environment-hardening"></a>Umgebungshärtung

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Kontinuierliche Überwachung ihrer Docker-Konfiguration

Azure Security Center identifiziert nicht verwaltete Container, die auf IaaS-Linux-VMs oder anderen Linux-Computern gehostet werden, auf denen Docker-Container ausgeführt werden. Security Center bewertet kontinuierlich die Konfigurationen dieser Container. Anschließend werden sie mit dem [Docker-Benchmark von Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/docker/) verglichen.

Security Center umfasst den gesamten Regelsatz des CIS-Docker-Benchmark und benachrichtigt Sie, sobald Ihre Container eine der Steuerungen nicht erfüllen. Werden Fehlkonfigurationen gefunden, generiert Security Center Sicherheitsempfehlungen. Verwenden Sie die Seite **Empfehlungen** in Security Center, um Empfehlungen anzuzeigen und Probleme zu beheben. Die CIS-Benchmarkprüfungen können nicht auf von AKS verwalteten Instanzen oder von Databricks verwalteten virtuellen Computern ausgeführt werden.

Ausführliche Informationen zu den möglichen relevanten Security Center-Empfehlungen für dieses Feature finden Sie im Abschnitt [Compute](recommendations-reference.md#recs-compute) der Referenztabelle zu den Empfehlungen.

Wenn Sie die Sicherheitsprobleme auf einem virtuellen Computer untersuchen, stellt Security Center zusätzliche Informationen zu den Containern auf dem Computer bereit. Zu diesen Informationen gehören die Docker-Version und Anzahl der auf dem Host ausgeführten Images. 

Um nicht verwaltete Container zu überwachen, die auf IaaS-Linux-VMs gehostet werden, aktivieren Sie den optionalen [Azure Defender für Server](defender-for-servers-introduction.md).


### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Fortlaufende Überwachung ihrer Kubernetes-Cluster
Security Center kann zusammen mit Azure Kubernetes Service (AKS) verwendet werden, dem verwalteten Containerorchestrierungsdienst von Microsoft für die Entwicklung, Bereitstellung und Verwaltung von Anwendungen in Containern.

AKS bietet Sicherheitssteuerungen und Einblicke in den Sicherheitsstatus ihrer Cluster. Security Center verwendet diese Features, um die Konfiguration Ihrer AKS-Cluster konstant zu überwachen und an Branchenstandards ausgerichtete Sicherheitsempfehlungen zu generieren.

Nachfolgend sehen Sie ein allgemeines Diagramm der Interaktion zwischen Azure Security Center, Azure Kubernetes Service und Azure Policy:

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Allgemeine Architektur der Interaktion zwischen Azure Security Center, dem Azure Kubernetes-Dienst und Azure Policy" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::

Sie können sehen, dass Security Center die folgenden Elemente empfängt und analysiert:

- Überwachungsprotokolle vom API-Server
- Unformatierte Sicherheitsereignisse vom Log Analytics-Agent

    > [!NOTE]
    > Derzeit wird die Installation des Log Analytics-Agents in Azure Kubernetes Service-Clustern, die in VM-Skalierungsgruppen ausgeführt werden, nicht unterstützt.

- Clusterkonfigurationsinformationen vom AKS-Cluster
- Workloadkonfiguration von Azure Policy (über das **Azure Policy-Add-On für Kubernetes**)

Ausführliche Informationen zu den möglichen relevanten Security Center-Empfehlungen für dieses Feature finden Sie im Abschnitt [Compute](recommendations-reference.md#recs-compute) der Referenztabelle zu den Empfehlungen.


###  <a name="workload-protection-best-practices-using-kubernetes-admission-control"></a>Bewährte Methoden zum Schutz von Workloads mithilfe der Kubernetes-Zugangssteuerung

Installieren Sie das **Azure Policy-Add-On für Kubernetes**, um verschiedene Empfehlungen zum Schutz der Workloads Ihrer Kubernetes-Container zu erhalten. Dieses Add-On kann auch automatisch bereitgestellt werden, wie unter [Aktivieren der automatischen Bereitstellung von Erweiterungen](security-center-enable-data-collection.md#enable-auto-provisioning-of-extensions) erläutert. Wenn die automatische Bereitstellung für das Add-On auf „Ein“ festgelegt ist, wird die Erweiterung standardmäßig in allen vorhandenen und zukünftigen Clustern aktiviert (sofern diese die Add-On-Installationsanforderungen erfüllen).

Wie auf [dieser Azure Policy-Seite für Kubernetes](../governance/policy/concepts/policy-for-kubernetes.md) erläutert, erweitert das Add-On den Open-Source-Webhook für den Zugangscontroller [Gatekeeper v3](https://github.com/open-policy-agent/gatekeeper) für den  [Open Policy-Agent](https://www.openpolicyagent.org/). Kubernetes-Zugangscontroller sind Plug-Ins, die die Art der Verwendung Ihrer Cluster erzwingen. Das Add-On registriert sich als Webhook bei der Kubernetes-Zugangssteuerung und ermöglicht das zentrale, konsistente Anwenden von Skalierungs- und Sicherheitsvorkehrungen in Ihren Clustern. 

Mit dem Add-On in Ihrem AKS-Cluster wird jede Anforderung an den Kubernetes-API-Server anhand der vordefinierten bewährten Methoden überwacht, bevor sie im Cluster persistent gespeichert wird. Anschließend können Sie das **Erzwingen** der bewährten Methoden konfigurieren und auf zukünftige Workloads anwenden. 

Beispielsweise können Sie vorschreiben, dass keine privilegierten Container erstellt werden sollen und dass zukünftige Anforderungen für diese Aktion blockiert werden sollen.

Weitere Informationen finden Sie unter [Schützen Ihrer Kubernetes-Workloads](kubernetes-workload-protections.md).


## <a name="run-time-protection-for-aks-nodes-and-clusters"></a>Laufzeitschutz für AKS-Knoten und -Cluster

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]



## <a name="next-steps"></a>Nächste Schritte

In dieser Übersicht haben Sie die Kernelemente der Containersicherheit in Azure Security Center kennengelernt. Verwandte Informationen finden Sie hier:

- [Einführung in Azure Defender für Kubernetes](defender-for-kubernetes-introduction.md)
- [Einführung in Azure Defender für Containerregistrierungen](defender-for-container-registries-introduction.md)