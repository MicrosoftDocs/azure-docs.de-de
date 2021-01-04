---
title: Konzepte – Sicherheit in Azure Kubernetes Service (AKS)
description: Erfahren Sie mehr über die Sicherheit in Azure Kubernetes Service (AKS), einschließlich Master- und Knoten-Kommunikation, Netzwerkrichtlinien und Kubernetes-Geheimnisse.
services: container-service
author: mlearned
ms.topic: conceptual
ms.date: 07/01/2020
ms.author: mlearned
ms.openlocfilehash: 1adf8370f55a0f6131eb4140c58fa4618e08127b
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686020"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Sicherheitskonzepte für Anwendungen und Cluster in Azure Kubernetes Service (AKS)

Damit Ihre Kundendaten während der Ausführung von Anwendungsworkloads in Azure Kubernetes Service (AKS) geschützt werden, kommt der Sicherheit des Clusters eine besondere Bedeutung zu. Kubernetes enthält Sicherheitskomponenten wie *Netzwerkrichtlinien* und *Geheimnisse*. Durch Azure kommen Komponenten wie Netzwerksicherheitsgruppen und orchestrierte Clusterupgrades hinzu. Diese Sicherheitskomponenten werden dann so miteinander kombiniert, dass auf dem AKS-Cluster jederzeit die aktuellen Betriebssystem-Sicherheitsupdates und Kubernetes-Versionen ausgeführt werden, der Datenverkehr zwischen den Pods geschützt und der Zugriff auf sensible Anmeldeinformationen gesichert ist.

In diesem Artikel werden die wichtigsten Konzepte vorgestellt, mit denen Sie Anwendungen in AKS schützen können:

- [Sicherheitskonzepte für Anwendungen und Cluster in Azure Kubernetes Service (AKS)](#security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks)
  - [Sicherheit der Masterkomponenten](#master-security)
  - [Knotensicherheit](#node-security)
    - [Computeisolation](#compute-isolation)
  - [Clusterupgrades](#cluster-upgrades)
    - [Absperren und Ausgleichen](#cordon-and-drain)
  - [Netzwerksicherheit](#network-security)
    - [Azure-Netzwerksicherheitsgruppen](#azure-network-security-groups)
  - [Kubernetes-Geheimnisse](#kubernetes-secrets)
  - [Nächste Schritte](#next-steps)

## <a name="master-security"></a>Sicherheit der Masterkomponenten

In AKS sind die Kubernetes-Masterkomponenten Bestandteil des verwalteten Diensts, der von Microsoft bereitgestellt wird. Jeder AKS-Cluster verfügt über seinen eigenen dedizierten Kubernetes-Master mit einem einzelnen Mandanten, um API-Server, Scheduler usw. bereitzustellen. Dieser Master wird von Microsoft verwaltet und gepflegt.

Standardmäßig verwendet der Kubernetes-API-Server eine öffentliche IP-Adresse und einen vollqualifizierten Domänennamen (FQDN). Sie können den Zugriff auf den API-Serverendpunkt mithilfe von [autorisierten IP-Adressbereichen][authorized-ip-ranges] einschränken. Alternativ können Sie einen vollständig [privaten Cluster][private-clusters] erstellen, um den Zugriff des API-Servers auf Ihr virtuelles Netzwerk einzuschränken.

Mithilfe der rollenbasierten Zugriffssteuerung von Kubernetes (Kubernetes RBAC) und Azure RBAC können Sie den Zugriff auf den API-Server steuern. Weitere Informationen finden Sie unter [Azure AD-Integration mit AKS][aks-aad].

## <a name="node-security"></a>Knotensicherheit

AKS-Knoten sind virtuelle Azure-Computer, die von Ihnen verwaltet und gepflegt werden. Linux-Knoten führen eine optimierte Ubuntu-Distribution mit der Moby-Containerruntime aus. Windows Server-Knoten führen eine optimierte Version von Windows Server 2019 aus. Auch sie verwenden die Moby-Containerruntime. Wenn ein AKS-Cluster erstellt oder zentral hochskaliert wird, werden die Knoten automatisch mit den aktuellen Betriebssystem-Sicherheitsupdates und -konfigurationen bereitgestellt.

Die Azure-Plattform wendet über Nacht automatisch Betriebssystem-Sicherheitspatches auf die Linux-Knoten an. Wenn ein Betriebssystem-Sicherheitsupdate für Linux einen Neustart des Hosts erfordert, wird dieser Neustart nicht automatisch ausgeführt. Sie können die Linux-Knoten manuell neu starten. Eine andere gängige Methode ist die Verwendung von [kured][kured], einem Open-Source-Neustartdaemon für Kubernetes. Kured wird als ein [DaemonSet][aks-daemonsets] ausgeführt und überwacht jeden Knoten auf das Vorhandensein einer Datei, die angibt, dass ein Neustart erforderlich ist. Neustarts werden clusterübergreifend verwaltet, wobei derselbe [Vorgang des Absperrens und Ausgleichens](#cordon-and-drain) wie bei einem Clusterupgrade angewendet wird.

Für Windows Server-Knoten werden die neuesten Updates von Windows Update nicht automatisch ausgeführt und angewendet. Sie sollten in regelmäßigen Abständen ein Upgrade für die Windows Server-Knotenpools in Ihrem AKS-Cluster durchführen, passend zum Windows Update-Freigabezyklus und Ihrem eigenen Validierungsprozess. Während dieses Upgrades werden Knoten erstellt, die das neueste Windows Server-Image und die neuesten Windows Server-Patches ausführen und die älteren Knoten entfernen. Weitere Informationen zu diesem Prozess finden Sie unter [Durchführen eines Upgrades für einen Knotenpool in AKS][nodepool-upgrade].

Knoten werden in einem Subnetz des privaten virtuellen Netzwerks ohne öffentliche IP-Adresse bereitgestellt. Zur Problembehandlung und Verwaltung ist SSH standardmäßig aktiviert. Dieser SSH-Zugriff ist nur über die interne IP-Adresse verfügbar.

Die Knoten verwenden Azure Managed Disks, um Speicher bereitzustellen. Bei den meisten VM-Knotengrößen handelt es sich um Premium-Datenträger, die von Hochleistungs-SSDs unterstützt werden. Die auf verwalteten Datenträgern gespeicherten Daten werden im Ruhezustand auf der Azure-Plattform automatisch verschlüsselt. Zur Verbesserung der Redundanz werden diese Datenträger außerdem sicher im Azure-Rechenzentrum repliziert.

Kubernetes-Umgebungen, ob in AKS oder an anderer Stelle, sind derzeit nicht völlig sicher vor feindlicher Verwendung mit mehreren Mandanten. Zusätzliche Sicherheitsfeatures wie *Podsicherheitsrichtlinien* oder differenziertere rollenbasierte Kubernetes-Zugriffssteuerung (Kubernetes RBAC) für Knoten erschweren Angriffe. Für echte Sicherheit bei der Ausführung feindlicher Workloads mit mehreren Mandanten ist jedoch ein Hypervisor die einzige Sicherheitsstufe, der Sie vertrauen sollten. Die Sicherheitsdomäne für Kubernetes wird zum gesamten Cluster und nicht zu einem einzelnen Knoten. Für diese Art von feindlichen Workloads mit mehreren Mandanten sollten Sie physisch isolierte Cluster verwenden. Weitere Informationen zu Möglichkeiten zur Isolierung von Workloads finden Sie unter [Bewährte Methoden für die Isolierung der Cluster in AKS][cluster-isolation].

### <a name="compute-isolation"></a>Computeisolation

 Bestimmte Workloads erfordern möglicherweise aufgrund von Konformitäts- oder gesetzlichen Anforderungen einen hohen Grad an Isolation von anderen Kundenworkloads. Für diese Workloads bietet Azure [isolierte virtuelle Computer](../virtual-machines/isolation.md), die als Agentknoten in einem AKS-Cluster verwendet werden können. Diese isolierten VMs sind für einen bestimmten Hardwaretyp isoliert und für einen einzelnen Kunden bestimmt. 

 Wenn Sie diese isolierten virtuellen Computer mit einem AKS-Cluster verwenden möchten, wählen Sie eine der [hier](../virtual-machines/isolation.md) aufgeführten Größen für isolierte virtuelle Computer als **Knotengröße** beim Erstellen eines AKS-Clusters oder Hinzufügen eines Knotenpools aus.


## <a name="cluster-upgrades"></a>Clusterupgrades

Aus Sicherheits- und Compliancegründen (oder zur Verwendung der neuesten Features) stellt Azure Tools bereit, um das Upgrade eines AKS-Clusters und seiner Komponenten zu orchestrieren. Diese Upgradeorchestrierung umfasst sowohl die Kubernetes-Master- als auch die Agent-Komponenten. Sie können eine [Liste der verfügbaren Kubernetes-Versionen](supported-kubernetes-versions.md) für Ihren AKS-Cluster anzeigen. Um den Upgradevorgang zu starten, geben Sie eine dieser verfügbaren Versionen an. Azure verwendet dann für jeden AKS-Knoten den sicheren Vorgang des Absperrens und Ausgleichens und führt das Upgrade aus.

### <a name="cordon-and-drain"></a>Absperren und Ausgleichen

Während des Upgrades werden AKS-Knoten einzeln vom Cluster abgesperrt, damit auf ihnen keine neuen Pods geplant werden. Die Knoten werden dann wie folgt ausgeglichenund aktualisiert:

- Ein neuer Knoten wird im Knotenpool bereitgestellt. Dieser Knoten führt das neueste Betriebssystemimage und die neuesten Patches aus.
- Einer der bereits vorhandenen Knoten wird für das Upgrade identifiziert. Pods auf diesem Knoten werden ordnungsgemäß beendet und auf den anderen Knoten im Knotenpool geplant.
- Der vorhandene Knoten wird aus dem AKS-Cluster gelöscht.
- Der nächste Knoten im Cluster wird mit der gleichen Methode abgesperrt und ausgeglichen, bis alle Knoten als Teil des Upgradeprozesses erfolgreich ersetzt wurden.

Weitere Informationen finden Sie unter [Aktualisieren eines AKS-Clusters][aks-upgrade-cluster].

## <a name="network-security"></a>Netzwerksicherheit

Für Konnektivität und Sicherheit bei lokalen Netzwerken können Sie Ihren AKS-Cluster in Subnetzen eines vorhandenen virtuellen Azure-Netzwerks bereitstellen. Diese virtuellen Netzwerke können über ein Azure-Site-to-Site-VPN oder eine Expressroute-Verbindung zurück zu Ihrem lokalen Netzwerk verfügen. Kubernetes-Eingangscontroller können mit privaten, internen IP-Adressen definiert werden, damit die Dienste nur über diese interne Netzwerkverbindung zugänglich sind.

### <a name="azure-network-security-groups"></a>Azure-Netzwerksicherheitsgruppen

Zum Filtern des Datenverkehrsflusses in virtuellen Netzwerken verwendet Azure Netzwerksicherheitsgruppen-Regeln. Diese Regeln bestimmen die Quell- und Ziel-IP-Adressbereiche, Ports und Protokolle, die für den Zugriff auf Ressourcen zugelassen oder abgelehnt werden. Standardregeln werden erstellt, um TLS-Datenverkehr zum Kubernetes-API-Server zu ermöglichen. Wenn Sie Dienste mit Lastenausgleich, Portzuordnungen oder Eingangsrouten erstellen, ändert AKS automatisch die Netzwerksicherheitsgruppe, damit der Datenverkehr entsprechend fließen kann.

Nehmen Sie in Fällen, in denen Sie Ihr eigenes Subnetz für Ihren AKS-Cluster bereitstellen und den Datenverkehrsfluss ändern möchten, keine Änderungen an der von AKS verwalteten Netzwerksicherheitsgruppe auf Subnetzebene vor. Sie können zusätzliche Netzwerksicherheitsgruppen auf Subnetzebene erstellen, um den Datenverkehrsfluss zu ändern, solange sie nicht den für die Verwaltung des Clusters erforderlichen Datenverkehr beeinträchtigen, z. B. den Zugriff auf das Lastenausgleichsmodul, die Kommunikation mit der Steuerungsebene und den [ausgehenden][aks-limit-egress-traffic] Datenverkehr.

### <a name="kubernetes-network-policy"></a>Kubernetes-Netzwerkrichtlinie

Zur Einschränkung von Netzwerkdatenverkehr zwischen Pods in Ihrem Cluster bietet AKS Unterstützung für [Kubernetes-Netzwerkrichtlinien][network-policy]. Mit Netzwerkrichtlinien können Sie den Zugriff auf bestimmte Netzwerkpfade im Cluster basierend auf Namespaces und Bezeichnungsselektoren zulassen oder verweigern.

## <a name="kubernetes-secrets"></a>Kubernetes-Geheimnisse

Ein Kubernetes-*Geheimnis* wird verwendet, um sensible Daten wie Anmeldeinformationen oder Schlüssel in Pods einzufügen. Zuerst erstellen Sie ein Geheimnis mit der Kubernetes-API. Wenn Sie Ihren Pod oder die Bereitstellung definieren, kann ein bestimmtes Geheimnis angefordert werden. Geheimnisse werden nur für Knoten bereitgestellt, die über einen eingeplanten Pod verfügen, der es benötigt, und das Geheimnis wird in *tmpfs* gespeichert, nicht auf den Datenträger geschrieben. Wenn der letzte Pod auf einem Knoten gelöscht wird, der ein Geheimnis benötigt, wird das Geheimnis aus dem Verzeichnis „tmpfs“ des Knotens gelöscht. Geheimnisse werden in einem bestimmten Namespace gespeichert, und nur Pods im gleichen Namespace können darauf zugreifen.

Die Verwendung von Geheimnissen reduziert die vertraulichen Informationen, die im YAML-Manifest für den Pod oder den Dienst definiert werden. Stattdessen fordern Sie im Rahmen Ihres YAML-Manifests das im Kubernetes-API-Server gespeicherte Geheimnis an. Mit diesem Ansatz erhält nur der spezielle Pod Zugriff auf das Geheimnis. Hinweis: Die unformatierten geheimen Manifestdateien enthalten die geheimen Daten im Base64-Format (weitere Einzelheiten finden Sie in der [offiziellen Dokumentation][secret-risks]). Aus diesem Grund sollte diese Datei wie vertrauliche Daten behandelt und nie in die Quellcodeverwaltung committet werden.

Kubernetes-Geheimnisse werden in „etcd“ gespeichert, einem verteilten Schlüssel-Wert-Speicher. Der etcd-Speicher wird vollständig von AKS verwaltet, und [Daten werden im Ruhezustand innerhalb der Azure Platform verschlüsselt][encryption-atrest]. 

## <a name="next-steps"></a>Nächste Schritte

Die ersten Schritte zum Sichern Ihrer AKS-Cluster sind unter [Aktualisieren eines AKS-Clusters][aks-upgrade-cluster] beschrieben.

Entsprechende bewährte Methoden finden Sie unter [Best Practices für Clustersicherheit und Upgrades in Azure Kubernetes Service (AKS)][operator-best-practices-cluster-security] und [Best Practices für Podsicherheit in Azure Kubernetes Service (AKS)][developer-best-practices-pod-security].

Weitere Informationen zu den wesentlichen Konzepten von Kubernetes und AKS finden Sie in den folgenden Artikeln:

- [Kubernetes/AKS: Cluster und Workloads][aks-concepts-clusters-workloads]
- [Kubernetes/AKS: Identität][aks-concepts-identity]
- [Kubernetes/AKS: virtuelle Netzwerke][aks-concepts-network]
- [Kubernetes/AKS: Speicher][aks-concepts-storage]
- [Kubernetes/AKS: Skalierung][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks
[encryption-atrest]: ../security/fundamentals/encryption-atrest.md

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: ./managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[aks-limit-egress-traffic]: limit-egress-traffic.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[authorized-ip-ranges]: api-server-authorized-ip-ranges.md
[private-clusters]: private-clusters.md
[network-policy]: use-network-policies.md
