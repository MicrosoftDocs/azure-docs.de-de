---
title: Bewährte Methoden für die Verwaltung der Identität
titleSuffix: Azure Kubernetes Service
description: Lernen Sie die Best Practices für Clusteroperator zum Verwalten der Authentifizierung und Autorisierung für Cluster in Azure Kubernetes Service (AKS) kennen.
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: a63a756448f9c7202c79c3b4625fc99d4a90dc52
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014056"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Best Practices für die Authentifizierung und Autorisierung in Azure Kubernetes Service (AKS)

Beim Bereitstellen und Verwalten von Clustern in Azure Kubernetes Service (AKS) müssen Sie Möglichkeiten zum Verwalten des Zugriffs auf Ressourcen und Dienste implementieren. Ohne diese Steuerungen haben Konten möglicherweise Zugriff auf Ressourcen und Dienste, die sie nicht benötigen. Es kann auch schwierig sein, nachzuverfolgen, welcher Satz von Anmeldeinformationen verwendet wurde, um Änderungen vorzunehmen.

Dieser Artikel zu Best Practices konzentriert sich darauf, wie ein Clusteroperator Zugriff und Identität für AKS-Cluster verwalten kann. In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
>
> * Authentifizieren von Benutzern von AKS-Clustern mit Azure Active Directory
> * Steuern des Zugriffs auf Ressourcen mit der rollenbasierten Zugriffssteuerung für Kubernetes (Kubernetes Role-Based Access Control, Kubernetes RBAC)
> * Verwenden Sie Azure RBAC, um den Zugriff auf die AKS-Ressource differenziert und auf die Kubernetes-API im großen Stil sowie auf kubeconfig zu steuern.
> * Verwenden einer verwalteten Identität zur Authentifizierung von Pods bei anderen Diensten

## <a name="use-azure-active-directory"></a>Verwenden von Azure Active Directory

**Best Practice-Anleitung**: Stellen Sie AKS-Cluster mit Azure AD-Integration bereit. Die Verwendung von Azure AD zentralisiert die Identitätsverwaltungskomponente. Jede Änderung von Benutzerkonto oder Gruppenstatus wird automatisch im Zugriff auf den AKS-Cluster aktualisiert. Verwenden Sie Rollen oder ClusterRoles und Bindungen, wie im nächsten Abschnitt erörtert, um Benutzer oder Gruppen auf ein Minimum an Berechtigungen einzuschränken.

Die Entwickler und Anwendungsbesitzer Ihres Kubernetes-Clusters benötigen Zugriff auf verschiedene Ressourcen. Kubernetes bietet keine Identitätsverwaltungslösung, um zu steuern, welche Benutzer mit welchen Ressourcen interagieren können. Stattdessen integrieren Sie Ihren Cluster in der Regel in eine vorhandene Identitätslösung. Azure Active Directory (AD) bietet eine Lösung zur Identitätsverwaltung für Unternehmen und kann in AKS-Cluster integriert werden.

Mit Clustern mit Azure AD-Integration in AKS erstellen Sie *Rollen* oder *ClusterRoles*, die Berechtigungen zum Zugriff auf Ressourcen definieren. Dann *binden* Sie die Rollen von Azure AD aus an Benutzer oder Gruppen. Diese rollenbasierte Zugriffssteuerung für Kubernetes (Kubernetes Role-Based Access Control, Kubernetes RBAC) wird im nächsten Abschnitt erläutert. Die Integration von Azure AD, und wie Sie den Zugriff auf Ressourcen steuern, wird im folgenden Diagramm gezeigt:

![Authentifizierung auf Clusterebene für die Azure Active Directory-Integration in AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. Der Entwickler authentifiziert sich mit Azure AD.
1. Der Azure AD-Tokenausstellungs-Endpunkt stellt das Zugriffstoken aus.
1. Der Entwickler führt eine Aktion mithilfe des Azure AD-Tokens durch, wie etwa `kubectl create pod`.
1. Kubernetes überprüft das Token in Azure Active Directory und ruft die Gruppenmitgliedschaften des Entwicklers ab.
1. Die rollenbasierte Zugriffssteuerung für Kubernetes (Kubernetes Role-Based Access Control, Kubernetes RBAC) und Clusterrichtlinien werden angewendet.
1. Ob die Anforderung des Entwicklers erfolgreich ist, hängt von der vorherigen Überprüfung der Azure AD-Gruppenmitgliedschaft sowie Kubernetes-RBAC und Richtlinien ab.

Wie Sie einen AKS-Cluster erstellen, der Azure AD verwendet, erfahren Sie unter [Integrieren von Azure Active Directory in Azure Kubernetes Service][aks-aad].

## <a name="use-kubernetes-role-based-access-control-kubernetes-rbac"></a>Verwenden der rollenbasierten Zugriffssteuerung für Kubernetes (Kubernetes Role-Based Access Control, Kubernetes RBAC)

**Best Practice-Anleitung**: Verwenden Sie Kubernetes-RBAC zum Definieren der Berechtigungen von Benutzern oder Gruppen für Ressourcen im Cluster. Erstellen von Rollen und Bindungen, die die Mindestberechtigungen zuweisen. Führen Sie die Integration in Azure AD durch, sodass jede Änderung von Benutzerstatus oder Gruppenmitgliedschaft automatisch aktualisiert wird, und der Zugriff auf Clusterressourcen aktuell ist.

In Kubernetes können Sie eine differenzierte Steuerung des Zugriffs auf Ressourcen im Cluster bereitstellen. Berechtigungen können auf Clusterebene oder für bestimmte Namespaces definiert werden. Sie können definieren, welche Ressourcen mit welchen Berechtigungen verwaltet werden können. Diese Rollen werden dann auf Benutzer oder Gruppen mit einer Bindung angewandt. Weitere Informationen zu *Rollen*, *Clusterrollen* und *Bindungen* finden Sie unter [Zugriffs- und Identitätsoptionen für Azure Kubernetes Service (AKS)][aks-concepts-identity].

Sie können beispielsweise eine Rolle erstellen, die vollen Zugriff auf Ressourcen im Namespace mit dem Namen *finance-app* gewährt, wie im folgenden Beispiel-YAML-Manifest gezeigt:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

Eine RoleBinding wird dann erstellt, die den Azure AD-Benutzer *developer1\@contoso.com* an die RoleBinding bindet, wie im folgenden YAML-Beispielmanifest gezeigt:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

Wenn *developer1\@contoso.com* für den AKS-Cluster authentifiziert wird, verfügt er über vollständige Berechtigungen für Ressourcen im *finance-app*-Namespace. So trennen Sie logisch den Zugriff auf Ressourcen und steuern ihn. Kubernetes-RBAC sollte in Verbindung mit Azure AD-Integration verwendet werden, wie im vorherigen Abschnitt erläutert.

Informationen zur Verwendung von Azure AD-Gruppen für die Steuerung des Zugriffs auf Kubernetes-Ressourcen per Kubernetes RBAC finden Sie unter [Steuern des Zugriffs auf Clusterressourcen per rollenbasierter Zugriffssteuerung und mit Azure Active Directory-Identitäten in Azure Kubernetes Service][azure-ad-rbac].

## <a name="use-azure-rbac"></a>Verwenden von Azure RBAC 
**Leitfaden für bewährte Methoden**: Verwenden Sie Azure RBAC, um die minimal erforderlichen Berechtigungen zu definieren, die Benutzer oder Gruppen für AKS-Ressourcen in einem oder mehreren Abonnements haben.

Für den vollständigen Betrieb eines AKS-Clusters sind zwei Zugriffsebenen erforderlich: 
1. Zugriff auf die AKS-Ressource in Ihrem Azure-Abonnement. Mit dieser Zugriffsebene können Sie z. B. die Skalierung oder das Upgrade Ihres Clusters mithilfe der AKS-APIs steuern und kubeconfig pullen.
Informationen zum Steuern des Zugriffs auf die AKS-Ressource und kubeconfig finden Sie unter [Beschränken des Zugriffs auf die Clusterkonfigurationsdatei](control-kubeconfig-access.md).

2. Zugriff auf die Kubernetes-API. Diese Zugriffsebene wird entweder durch [Kubernetes RBAC](#use-kubernetes-role-based-access-control-kubernetes-rbac) (herkömmlich) oder durch die Integration von Azure RBAC in AKS für die Kubernetes-Autorisierung gesteuert.
Informationen zur differenzierten Vergabe von Berechtigungen für die Kubernetes-API mit Azure RBAC finden Sie unter [Verwenden von Azure RBAC für Kubernetes-Autorisierung](manage-azure-rbac.md).

## <a name="use-pod-identities"></a>Verwenden von Podidentitäten

**Best Practice-Anleitung**: Verwenden Sie keine festen Anmeldeinformationen in Pods oder Containerimages, da sie offengelegt oder missbraucht werden können. Verwenden Sie stattdessen die Podidentitäten, um automatisch mit einer zentralen Azure AD-Identitätslösung Zugriff anzufordern. Podidentitäten sind nur für die Verwendung mit Linux-Pods und -Containerimages vorgesehen.

Wenn Pods Zugriff auf andere Azure-Dienste wie Cosmos DB, Key Vault oder Blob Storage benötigen, benötigt der Pod Anmeldeinformationen für den Zugriff. Diese Anmeldeinformationen für den Zugriff könnten mit dem Containerimage definiert oder als Kubernetes-Geheimnis eingefügt werden, müssen aber manuell erstellt und zugewiesen werden. Häufig werden die Anmeldeinformationen Pods übergreifend wiederverwendet und nicht regelmäßig rotiert.

Mit verwalteten Identitäten für Azure-Ressourcen (die derzeit als ein zugeordnetes AKS-Open-Source-Projekt implementiert sind) können Sie über Azure AD automatisch Zugriff auf Dienste anfordern. Sie definieren nicht manuell Anmeldeinformationen für Pods, stattdessen fordern diese ein Zugriffstoken in Echtzeit an und können es nur für den Zugriff auf die ihnen zugewiesenen Dienste verwenden. In AKS werden zwei Komponenten vom Clusteroperator bereitgestellt, um Pods die Verwendung verwalteter Identitäten zu ermöglichen:

* **Der Node Management Identity-Server (NMI)** ist ein Pod, der auf jedem Knoten im AKS-Cluster als DaemonSet ausgeführt wird. Der NMI-Server überwacht Podanforderungen an Azure-Dienste.
* **Der Managed Identity Controller (MIC)** ist ein zentraler Pod mit Berechtigungen zur Abfrage des Kubernetes-API-Servers und überprüft, ob eine Azure-Identitätszuordnung vorliegt, die einem Pod entspricht.

Wenn Pods Zugriff auf einen Azure-Dienst anfordern, leiten Netzwerkregeln den Datenverkehr zum Node Management Identity-Server (NMI) um. Der NMI-Server identifiziert Pods, die den Zugriff auf Azure-Dienste basierend auf ihrer Remoteadresse anfordern, und fragt den Managed Identity Controller (MIC) ab. Der MIC prüft, ob Azure-Identitätszuordnungen im AKS-Cluster vorliegen, und der NMI-Server fordert dann basierend auf der Identitätszuordnung des Pods von Azure Active Directory (AD) ein Zugriffstoken an. Azure AD bietet Zugriff auf den NMI-Server, der an den Pod zurückgegeben wird. Dieses Zugriffstoken kann dann vom Pod verwendet werden, um Zugriff auf Azure-Dienste anzufordern.

Im folgenden Beispiel erstellt ein Entwickler einen Pod, der eine verwaltete Identität verwendet, um Zugriff auf Azure SQL-Datenbank anzufordern:

![Mit Podidentitäten kann ein Pod automatisch Zugriff auf andere Dienste anfordern.](media/operator-best-practices-identity/pod-identities.png)

1. Der Clusteroperator erstellt zunächst ein Dienstkonto, das verwendet werden kann, um Identitäten zuzuordnen, wenn Pods Zugriff auf Dienste anzufordern.
1. NMI-Server und MIC werden zum Weiterleiten von Podanforderungen von Zugriffstoken für den Azure AD-Zugriff bereitgestellt.
1. Ein Entwickler stellt einen Pod mit einer verwalteten Identität bereit, die ein Zugriffstoken über den NMI-Server anfordert.
1. Das Token wird an den Pod zurückgegeben und für den Zugriff auf Azure SQL-Datenbank verwendet.

> [!NOTE]
> Verwaltete Podidentitäten sind ein Open-Source-Projekt, das vom technischen Support von Azure nicht unterstützt wird.

Wie Sie Podidentitäten verwenden, erfahren Sie unter [Azure Active Directory identities for Kubernetes applications][aad-pod-identity] (Azure Active Directory-Identitäten für Kubernetes-Anwendungen).

## <a name="next-steps"></a>Nächste Schritte

Dieser Best Practices-Artikel konzentriert sich auf Authentifizierung und Autorisierung für Ihre Cluster und Ressourcen. Um einige dieser Best Practices zu implementieren, lesen Sie folgende Artikel:

* [Integrieren von Azure Active Directory in AKS][aks-aad]
* [Verwenden von verwalteten Identitäten für Azure-Ressourcen mit AKS][aad-pod-identity]

Weitere Informationen zu Clustervorgängen in AKS finden Sie in den folgenden Best Practices:

* [Best Practices für Clusterisolierung in Azure Kubernetes Service (AKS)][aks-best-practices-cluster-isolation]
* [Best Practices für grundlegende Schedulerfunktionen in Azure Kubernetes Service (AKS)][aks-best-practices-scheduler]
* [Best Practices für erweiterte Schedulerfunktionen in Azure Kubernetes Service (AKS)][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md
