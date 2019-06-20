---
title: Einschränken des ausgehenden Datenverkehrs in Azure Kubernetes Service (AKS)
description: Erfahren Sie, welche Ports und Adressen zur Steuerung des ausgehenden Datenverkehrs in Azure Kubernetes Service (AKS) erforderlich sind.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: b5a203150906758bde33431a1dab717e090f2e28
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475571"
---
# <a name="preview---limit-egress-traffic-for-cluster-nodes-and-control-access-to-required-ports-and-services-in-azure-kubernetes-service-aks"></a>Vorschau: Einschränken des ausgehenden Datenverkehrs für Clusterknoten und Steuern des Zugriffs auf erforderliche Ports und Dienste in Azure Kubernetes Service (AKS)

Standardmäßig haben AKS-Cluster uneingeschränkten ausgehenden Internetzugriff. Diese Ebene des Netzwerkzugriffs ermöglicht, dass ausgeführte Knoten und Dienste nach Bedarf auf externe Ressourcen zugreifen können. Wenn Sie den ausgehenden Datenverkehr einschränken möchten, muss eine begrenzte Anzahl von Ports und Adressen zugänglich sein, um fehlerfreie Clusterwartungsaufgaben verwalten zu können. Der Cluster wird dann so konfiguriert, dass nur Containerimages des Basissystems aus Microsoft Container Registry (MCR) oder Azure Container Registry (ACR), jedoch aus keinen externen öffentlichen Repositorys verwendet werden.

In diesem Artikel wird erläutert, welche Netzwerkports und vollqualifizierten Domänennamen (FQDNs) erforderlich oder optional sind, wenn Sie den ausgehenden Datenverkehr in einem AKS-Cluster einschränken.  Diese Funktion steht derzeit als Vorschau zur Verfügung.

> [!IMPORTANT]
> AKS-Previewfunktionen stehen gemäß dem Self-Service- und Aktivierungsprinzip zur Verfügung. Sie werden zum Sammeln von Feedback und Fehlern mithilfe unserer Community bereitgestellt. In der Vorschauversion sind diese Features nicht für den Einsatz in der Produktion vorgesehen. Features in der öffentlichen Vorschau unterliegen dem Prinzip des „bestmöglichen Supports“. Unterstützung durch die Teams für den technischen AKS-Support steht nur während der Geschäftszeiten in der Zeitzone „Pacific Standard Time“ (PST) zur Verfügung. Weitere Informationen hierzu finden Sie in den folgenden Supportartikeln:
>
> * [Unterstützungsrichtlinien für Azure Kubernetes Service][aks-support-policies]
> * [Häufig gestellte Fragen zum Azure-Support][aks-faq]

## <a name="before-you-begin"></a>Voraussetzungen

Azure CLI-Version 2.0.61 oder höher muss installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu finden. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Installieren von Azure CLI 2.0][install-azure-cli] Informationen dazu.

Um einen AKS-Cluster zu erstellen, für den der ausgehende Datenverkehr eingeschränkt werden kann, aktivieren Sie zuerst ein Featureflag für Ihr Abonnement. Mit dieser Featureregistrierung werden alle AKS-Cluster, die Sie erstellen, so konfiguriert, dass Containerimages des Basissystems aus MCR oder ACR verwendet werden. Um das Featureflag *AKSLockingDownEgressPreview* zu registrieren, verwenden Sie den Befehl [az feature register][az-feature-register], wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az feature register --name AKSLockingDownEgressPreview --namespace Microsoft.ContainerService
```

Es dauert einige Minuten, bis der Status *Registered (Registriert)* angezeigt wird. Sie können den Registrierungsstatus mithilfe des Befehls [az feature list][az-feature-list] überprüfen:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSLockingDownEgressPreview')].{Name:name,State:properties.state}"
```

Wenn der Vorgang abgeschlossen ist, können Sie die Registrierung des *Microsoft.ContainerService*-Ressourcenanbieters mit dem Befehl [az provider register][az-provider-register] aktualisieren:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="egress-traffic-overview"></a>Übersicht über ausgehenden Datenverkehr

Zu Verwaltungs- und Betriebszwecken müssen Knoten in einem AKS-Cluster auf bestimmte Ports und vollqualifizierte Domänennamen (FQDNs) zugreifen. Mögliche Aktionen sind die Kommunikation mit dem API-Server oder das Herunterladen und anschließende Installieren von Kernkomponenten des Kubernetes-Clusters und Sicherheitsupdates für Knoten. Standardmäßig ist der ausgehende Internetdatenverkehr für Knoten in einem AKS-Cluster nicht eingeschränkt. Der Cluster kann Containerimages des Basissystems aus externen Repositorys pullen.

Um die Sicherheit Ihres AKS-Clusters zu erhöhen, können Sie den ausgehenden Datenverkehr einschränken. Der Cluster wird so konfiguriert, dass Containerimages des Basissystems aus MCR oder ACR gepullt werden. Wenn Sie den ausgehenden Datenverkehr auf diese Weise einschränken, müssen Sie bestimmte Ports und FQDNs definieren, damit AKS-Knoten ordnungsgemäß mit erforderlichen externen Diensten kommunizieren können. Ohne diese autorisierten Ports und FQDNs können AKS-Knoten nicht mit dem API-Server kommunizieren und keine Kernkomponenten installieren.

Sie können [Azure Firewall][azure-firewall] oder eine Firewallappliance eines Drittanbieters verwenden, um den ausgehenden Datenverkehr zu schützen und diese erforderlichen Ports und Adressen zu definieren.

In AKS gibt es zwei Gruppen von Ports und Adressen:

* Unter [Erforderliche Ports und Adressen für AKS-Cluster](#required-ports-and-addresses-for-aks-clusters) werden die Mindestanforderungen für autorisierten ausgehenden Datenverkehr erläutert.
* Die [optionalen empfohlenen Adressen und Ports für AKS-Cluster](#optional-recommended-addresses-and-ports-for-aks-clusters) sind nicht in allen Fällen erforderlich, die Integration in andere Dienste wie z. B. Azure Monitor funktioniert jedoch ohne sie nicht ordnungsgemäß. Überprüfen Sie diese Liste der optionalen Ports und FQDNs, und autorisieren Sie alle in Ihrem AKS-Cluster verwendeten Dienste und Komponenten.

> [!NOTE]
> Die Einschränkung des ausgehenden Datenverkehrs funktioniert nur bei neuen AKS-Clustern, die erstellt werden, nachdem Sie die Registrierung des Featureflags aktiviert haben. Bei vorhandenen Clustern müssen Sie mit dem Befehl `az aks upgrade` [ein Clusterupgrade ausführen][aks-upgrade], bevor Sie den ausgehenden Datenverkehr einschränken.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Erforderliche Ports und Adressen für AKS-Cluster

Die folgenden ausgehenden Ports und Netzwerkregeln sind für einen AKS-Cluster erforderlich:

* TCP-Port *443*
* Die TCP-Ports *9000* und *22* für die Kommunikation zwischen dem Tunnelfrontpod und dem Tunnelende auf dem API-Server.

Die folgenden vollqualifizierten Domänennamen und Anwendungsregeln sind erforderlich:

| FQDN                      | Port      | Zweck      |
|---------------------------|-----------|----------|
| *.azmk8s.io               | HTTPS:443,22,9000 | Diese Adresse ist der Endpunkt des API-Servers. |
| aksrepos.azurecr.io       | HTTPS: 443 | Diese Adresse ist für den Zugriff auf Images in Azure Container Registry (ACR) erforderlich. |
| *.blob.core.windows.net   | HTTPS: 443 | Diese Adresse ist der Back-End-Speicher für in ACR gespeicherte Images. |
| mcr.microsoft.com         | HTTPS: 443 | Diese Adresse ist für den Zugriff auf Images in Microsoft Container Registry (MCR) erforderlich. |
| management.azure.com      | HTTPS: 443 | Diese Adresse ist für GET/PUT-Vorgänge in Kubernetes erforderlich. |
| login.microsoftonline.com | HTTPS: 443 | Diese Adresse ist für die Azure Active Directory-Authentifizierung erforderlich. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Optionale empfohlene Adressen und Ports für AKS-Cluster

Die folgenden ausgehenden Ports und Netzwerkregeln sind für die ordnungsgemäße Ausführung von AKS-Clustern nicht erforderlich, werden jedoch empfohlen:

* UDP-Port *123* für die NTP-Zeitsynchronisierung
* UDP-Port *53* für DNS

Die folgenden vollqualifizierten Domänennamen und Anwendungsregeln werden für die ordnungsgemäße Ausführung von AKS-Clustern empfohlen:

| FQDN                                    | Port      | Zweck      |
|-----------------------------------------|-----------|----------|
| *.ubuntu.com                            | HTTP: 80   | Über diese Adresse können die Linux-Clusterknoten die erforderlichen Sicherheitspatches und Updates herunterladen. |
| packages.microsoft.com                  | HTTPS: 443 | Diese Adresse ist das Microsoft-Paketrepository, das für zwischengespeicherte *apt-get*-Vorgänge verwendet wird. |
| dc.services.visualstudio.com            | HTTPS: 443 | Empfohlen für korrekte Metriken und Überwachung mithilfe von Azure Monitor. |
| *.opinsights.azure.com                  | HTTPS: 443 | Empfohlen für korrekte Metriken und Überwachung mithilfe von Azure Monitor. |
| *.monitoring.azure.com                  | HTTPS: 443 | Empfohlen für korrekte Metriken und Überwachung mithilfe von Azure Monitor. |
| gov-prod-policy-data.trafficmanager.net | HTTPS: 443 | Diese Adresse wird für den ordnungsgemäßen Betrieb von Azure Policy (derzeit als Vorschauversion in AKS) verwendet. |
| apt.dockerproject.org                   | HTTPS: 443 | Diese Adresse wird für die ordnungsgemäße Treiberinstallation und -ausführung für GPU-basierte Knoten verwendet. |
| nvidia.github.io                        | HTTPS: 443 | Diese Adresse wird für die ordnungsgemäße Treiberinstallation und -ausführung für GPU-basierte Knoten verwendet. |

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, welche Ports und Adressen zugelassen werden müssen, wenn Sie den ausgehenden Datenverkehr für den Cluster einschränken. Sie können auch definieren, wie die Pods selbst kommunizieren können und welche Einschränkungen für sie im Cluster gelten. Weitere Informationen finden Sie unter [Sicherer Datenverkehr zwischen Pods durch Netzwerkrichtlinien in Azure Kubernetes Service (AKS)][network-policy].

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
