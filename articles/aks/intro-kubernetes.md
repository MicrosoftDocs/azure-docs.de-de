---
title: Einführung in Azure Kubernetes Service
description: Lernen Sie die Features und Vorteile von Azure Kubernetes Service zur Bereitstellung und Verwaltung containerbasierter Anwendungen in Azure kennen.
services: container-service
ms.topic: overview
ms.date: 02/09/2021
ms.custom: mvc
ms.openlocfilehash: 1505366d9a91eac596b21804f93abb8245a84605
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590007"
---
# <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

Azure Kubernetes Service (AKS) vereinfacht die Bereitstellung eines Managed Kubernetes-Clusters in Azure, indem ein Großteil der Komplexität und des betrieblichen Aufwands nach Azure ausgelagert wird. Azure führt als gehosteter Kubernetes-Dienst wichtige Aufgaben für Sie durch, z. B. die Systemüberwachung und Wartung.  

Da die Kubernetes-Mastereinheiten von Azure verwaltet werden, müssen Sie sich nur um die Verwaltung und Wartung der Agent-Knoten kümmern. Als Managed Kubernetes-Dienst ist AKS daher kostenlos. Sie zahlen nur für die Agent-Knoten in den Clustern, nicht für die Master.  

Sie können einen AKS-Cluster mit dem Azure-Portal, der Azure-Befehlszeilenschnittstelle oder vorlagengesteuerten Bereitstellungsoptionen wie Resource Manager-Vorlagen und Terraform erstellen. Wenn Sie einen AKS-Cluster bereitstellen, werden der Kubernetes-Master und alle Knoten für Sie bereitgestellt und konfiguriert. Zusätzliche Features wie erweiterte Netzwerke, Azure Active Directory-Integration und Überwachung können ebenfalls im Rahmen der Bereitstellung konfiguriert werden. Windows Server-Container werden in AKS unterstützt.

Weitere Informationen zu Kubernetes-Grundlagen finden Sie unter [Grundlegende Kubernetes-Konzepte für AKS][concepts-clusters-workloads].

Befolgen Sie zum Einstieg die AKS-Schnellstartanleitung [im Azure-Portal][aks-portal] oder [mit der Azure CLI][aks-cli].

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="access-security-and-monitoring"></a>Zugriff, Sicherheit und Überwachung

Zur Verbesserung der Sicherheit und Verwaltung ermöglicht AKS die Integration in Azure Active Directory (Azure AD) sowie Folgendes:
* Verwenden der rollenbasierten Zugriffssteuerung für Kubernetes (Kubernetes Role-Based Access Control, Kubernetes RBAC) 
* Überwachen der Integrität Ihres Clusters und der Ressourcen

### <a name="identity-and-security-management"></a>Identitäts- und Sicherheitsverwaltung

AKS unterstützt [Kubernetes RBAC][kubernetes-rbac], damit der Zugriff auf Clusterressourcen eingeschränkt werden kann. Mit Kubernetes RBAC können Sie den Zugriff auf Kubernetes-Ressourcen und -Namespaces sowie die zugehörigen Berechtigungen steuern.  

Sie können auch einen AKS-Cluster für die Azure AD-Integration konfigurieren. Mit der Azure AD-Integration können Sie den Kubernetes-Zugriff basierend auf der vorhandenen Identität und Gruppenmitgliedschaft konfigurieren. Für Ihre vorhandenen Azure AD-Benutzer und -Gruppen kann eine integrierte Anmeldeerfahrung bereitgestellt und der Zugriff auf AKS-Ressourcen gewährt werden.  

Weitere Informationen zur Identität finden Sie unter [Zugriffs- und Identitätsoptionen für AKS][concepts-identity].

Wie Sie Ihre AKS-Cluster schützen, erfahren Sie unter [Integrieren von Azure Active Directory in AKS – Vorschauversion][aks-aad].

### <a name="integrated-logging-and-monitoring"></a>Integrierte Protokollierung und Überwachung

Mit Azure Monitor für die Containerintegrität werden Metriken zum Arbeitsspeicher und zur Prozessorleistung aus Containern, Knoten und Controllern in Ihrem AKS-Cluster und in den bereitgestellten Anwendungen erfasst. Sie können sowohl die Containerprotokolle als auch die [Kubernetes-Masterprotokolle][aks-master-logs] überprüfen. Diese Überwachungsdaten werden in Ihrem Azure Log Analytics-Arbeitsbereich gespeichert und stehen über das Azure-Portal, die Azure CLI und einen REST-Endpunkt zur Verfügung.

Weitere Informationen finden Sie unter [Überwachen der Integrität von Azure Kubernetes Service-Containern (AKS) (Vorschauversion)][container-health].

## <a name="clusters-and-nodes"></a>Cluster und Knoten

AKS-Knoten werden auf virtuellen Azure-Computern (VMs) ausgeführt. Mit AKS-Knoten können Sie eine Verbindung des Speichers mit Knoten und Pods herstellen, Clusterkomponenten aktualisieren und GPUs verwenden. AKS unterstützt Kubernetes-Cluster, in denen mehrere Knotenpools ausgeführt werden, um gemischte Betriebssysteme und Windows Server-Container zu unterstützen.  

Weitere Informationen zu den Funktionen von Kubernetes-Clustern, -Knoten und -Knotenpools finden Sie unter [Wichtige Konzepte zu Kubernetes für AKS][concepts-clusters-workloads].

### <a name="cluster-node-and-pod-scaling"></a>Clusterknoten und Skalierung von Pods

Wenn sich die Nachfrage nach Ressourcen ändert, kann die Anzahl der Clusterknoten oder Pods, die Ihre Dienste ausführen, automatisch hoch- oder herunterskaliert werden. Sie können sowohl die horizontale automatische Podskalierungsfunktion als auch die Clusterskalierungsfunktion verwenden. Mit diesem Skalierungsansatz wird der AKS-Cluster automatisch den Anforderungen angepasst, sodass nur die erforderlichen Ressourcen ausgeführt werden.

Weitere Informationen finden Sie unter [Skalieren eines Azure Kubernetes Service-Clusters (AKS)][aks-scale].

### <a name="cluster-node-upgrades"></a>Upgrades für Clusterknoten

AKS verfügt über mehrere Kubernetes-Versionen. Wenn in AKS neue Versionen verfügbar werden, können Sie mithilfe des Azure-Portals oder der Azure CLI ein Clusterupgrade ausführen. Während des Upgradevorgangs werden die Knoten sorgfältig isoliert und ausgeglichen, um ausgeführte Anwendungen möglichst wenig zu beeinträchtigen.  

Weitere Informationen zu Lebenszyklusversionen finden Sie unter [Unterstützte Kubernetes-Versionen in Azure Kubernetes Service (AKS)][aks-supported versions]. Informationen zu Aktualisierungsschritten finden Sie unter [Durchführen eines Upgrades für einen Azure Kubernetes Service-Cluster (AKS)][aks-upgrade].

### <a name="gpu-enabled-nodes"></a>GPU-fähige Knoten

AKS unterstützt die Erstellung GPU-fähiger Knotenpools. Azure bietet derzeit virtuelle Computer mit einzelner GPU oder mit mehreren GPUs. GPU-fähige virtuelle Computer sind für rechenintensive, grafikintensive und visualisierungsorientierte Workloads vorgesehen.

Weitere Informationen finden Sie unter [Verwenden von GPUs in AKS][aks-gpu].

### <a name="confidential-computing-nodes-public-preview"></a>Confidential Computing-Knoten (Public Preview)

AKS unterstützt die Erstellung von Pools mit Intel SGX-basierten Confidential Computing-Knoten (DCSv2-VMs). Mit Confidential Computing-Knoten können Container in einer hardwarebasierten vertrauenswürdigen Ausführungsumgebung (Enklave) ausgeführt werden. Die Isolation zwischen Containern in Kombination mit einer Sicherstellung der Codeintegrität durch Nachweise kann ein Beitrag zu Ihrer Defense-in-Depth-Strategie für die Containersicherheit sein. Für Confidential Computing-Knoten werden sowohl vertrauliche Container (vorhandene Docker-Apps) als auch für Enklaven geeignete Container unterstützt.

Weitere Informationen finden Sie unter [Confidential Computing-Knoten in AKS][conf-com-node].

### <a name="storage-volume-support"></a>Unterstützung von Speichervolumes

Um Workloads von Anwendungen zu unterstützen, können Sie die Speichervolumes für persistente Daten bereitstellen. Sie können sowohl statische als auch dynamische Volumes verwenden. Je nachdem, wie viele verbundene Pods die Speichervolumes gemeinsam nutzen sollen, können Sie entweder für den Zugriff auf einen einzelnen Pod durch Azure-Datenträger unterstützten Speicher oder für den gleichzeitigen Zugriff auf mehrere Pods durch Azure Files unterstützten Speicher verwenden.

Weitere Informationen finden Sie unter [Speicheroptionen für Anwendungen in AKS][concepts-storage].

Erste Schritte mit dynamischen persistenten Volumes unter Verwendung von [Azure-Datenträgern][azure-disk] oder [Azure Files][azure-files].

## <a name="virtual-networks-and-ingress"></a>Virtuelle Netzwerke und Eingang

Ein AKS-Cluster kann in einem vorhandenen virtuellen Netzwerk bereitgestellt werden. Bei dieser Konfiguration wird jedem Pod im Cluster eine IP-Adresse im virtuellen Netzwerk zugewiesen, und es ist jeweils eine direkte Kommunikation mit anderen Pods im Cluster und anderen Knoten im virtuellen Netzwerk möglich. Pods können auch eine Verbindung mit anderen Diensten eines mittels Peering verbundenen virtuellen Netzwerks sowie mit lokalen Netzwerken über ExpressRoute- oder S2S-VPN-Verbindungen (Site-to-Site) herstellen.  

Weitere Informationen finden Sie unter [Netzwerkkonzepte für Anwendungen in AKS][aks-networking].

### <a name="ingress-with-http-application-routing"></a>Eingang mit HTTP-Anwendungsrouting

Das Add-On für das HTTP-Anwendungsrouting ermöglicht einen einfachen Zugriff auf Anwendungen, die in Ihrem AKS-Cluster bereitgestellt sind. Bei aktiviertem HTTP-Anwendungsrouting konfiguriert die Lösung einen Eingangscontroller in Ihrem AKS-Cluster.  

Im Rahmen der Anwendungsbereitstellung werden öffentlich zugängliche DNS-Namen automatisch konfiguriert. Das HTTP-Anwendungsrouting richtet eine DNS-Zone ein und integriert sie in den AKS-Cluster. Anschließend können Sie Kubernetes-Eingangsressourcen wie gewohnt bereitstellen.  

Informationen zu den ersten Schritten mit eingehendem Datenverkehr finden Sie unter [HTTP-Anwendungsrouting][aks-http-routing].

## <a name="development-tooling-integration"></a>Integration von Entwicklungstools

Kubernetes verfügt über ein umfassendes Ökosystem mit Entwicklungs- und Verwaltungstools, die nahtlos mit AKS genutzt werden können. Zu diesen Tools gehören auch Helm und die Kubernetes-Erweiterung für Visual Studio Code. Diese Tools funktionieren nahtlos mit AKS.  

Darüber hinaus verfügt Azure über mehrere Tools zur Optimierung von Kubernetes, z. B. Azure Dev Spaces und DevOps Starter.  

Azure Dev Spaces ermöglicht eine schnelle, iterative Kubernetes-Bereitstellung für Teams. Container lassen sich mit minimalem Konfigurationsaufwand direkt in AKS ausführen und debuggen. Informationen zu ersten Schritten finden Sie unter [Azure Dev Spaces][azure-dev-spaces].

DevOps Starter bietet eine Lösung, mit der Sie vorhandenen Code und Git-Repositorys ganz einfach in Azure einbinden können. Mit DevOps Starter wird automatisch Folgendes durchgeführt:
* Erstellen von Azure-Ressourcen (z. B. AKS) 
* Konfigurieren einer Releasepipeline in Azure DevOps Services, die eine Buildpipeline für CI enthält 
* Einrichten einer Releasepipeline für CD 
* Generieren einer Azure Application Insights-Ressource für die Überwachung 

Weitere Informationen finden Sie unter [Übersicht über DevOps Starter][azure-devops].

## <a name="docker-image-support-and-private-container-registry"></a>Docker-Imageunterstützung und private Containerregistrierung

AKS unterstützt das Docker-Imageformat. Zur privaten Speicherung Ihrer Docker-Images können Sie AKS in die Azure Container Registry (ACR) integrieren.

Wie Sie private Imagespeicher erstellen, erfahren Sie unter [Einführung in private Docker-Containerregistrierungen in Azure][acr-docs].

## <a name="kubernetes-certification"></a>Kubernetes-Zertifizierung

AKS wurde als Kubernetes-konform zertifiziert (CNCF).

## <a name="regulatory-compliance"></a>Compliance

AKS ist mit SOC, ISO, PCI-DSS und HIPAA konform. Weitere Informationen finden Sie in der [Übersicht über Microsoft Azure-Compliance][compliance-doc].

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Bereitstellung und Verwaltung von AKS in der Azure CLI-Schnellstartanleitung.

> [!div class="nextstepaction"]
> [Schnellstart: Bereitstellen eines AKS-Clusters (Azure Kubernetes Service) über die Azure-Befehlszeilenschnittstelle][aks-cli]

<!-- LINKS - external -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[compliance-doc]: https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./azure-ad-integration-cli.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./concepts-network.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: ../dev-spaces/index.yml
[azure-devops]: ../devops-project/overview.md
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../azure-monitor/containers/container-insights-overview.md
[aks-master-logs]: view-master-logs.md
[aks-supported versions]: supported-kubernetes-versions.md
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[kubernetes-rbac]: concepts-identity.md#kubernetes-role-based-access-control-kubernetes-rbac
[concepts-identity]: concepts-identity.md
[concepts-storage]: concepts-storage.md
[conf-com-node]: ../confidential-computing/confidential-nodes-aks-overview.md
