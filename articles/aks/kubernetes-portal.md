---
title: Zugreifen auf Kubernetes-Ressourcen über das Azure-Portal (Vorschau)
description: Erfahren Sie, wie Sie mit Kubernetes-Ressourcen interagieren, um einen Azure Kubernetes Service-Cluster (AKS) über das Azure-Portal zu verwalten.
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: cfd09e469de68a1eee7440773347e9fe58bf8619
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571622"
---
# <a name="access-kubernetes-resources-from-the-azure-portal-preview"></a>Zugreifen auf Kubernetes-Ressourcen über das Azure-Portal (Vorschau)

Das Azure-Portal umfasst eine Kubernetes-Ressourcenanzeige (Vorschau) für den einfachen Zugriff auf die Kubernetes-Ressourcen in Ihrem Azure Kubernetes Service-Cluster (AKS). Durch das Anzeigen von Kubernetes-Ressourcen aus dem Azure-Portal wird das Wechseln des Kontexts zwischen dem Azure-Portal und dem `kubectl`-Befehlszeilentool verringert, wodurch die Erfahrung des Anzeigens und Bearbeitens Ihrer Kubernetes-Ressourcen optimiert wird. Die Ressourcenanzeige enthält derzeit mehrere Ressourcentypen, z. B. Bereitstellungen, Pods und Replikatgruppen.

Die Kubernetes-Ressourcenansicht im Azure-Portal ersetzt das veraltete [AKS-Dashboard-Add-On][kubernetes-dashboard].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Voraussetzungen

Um Kubernetes-Ressourcen im Azure-Portal anzuzeigen, benötigen Sie einen AKS-Cluster. Jeder Cluster wird unterstützt, doch wenn Sie eine Azure Active Directory-Integration (Azure AD) verwenden, muss Ihr Cluster die [von AKS verwaltete Azure AD-Integration][aks-managed-aad] verwenden. Wenn Ihr Cluster Legacy-Azure AD verwendet, können Sie ein Upgrade Ihres Clusters im Portal oder mithilfe der [Azure CLI][cli-aad-upgrade] durchführen.

## <a name="view-kubernetes-resources"></a>Anzeigen von Kubernetes-Ressourcen

Um die Kubernetes-Ressourcen anzuzeigen, navigieren Sie im Azure-Portal zu Ihrem AKS-Cluster. Der Navigationsbereich auf der linken Seite wird verwendet, um auf Ihre Ressourcen zuzugreifen. Die Ressource umfasst Folgendes:

- **Namespaces** zeigt die Namespaces Ihres Clusters an. Der Filter am Anfang der Namespaceliste bietet eine schnelle Möglichkeit zum Filtern und Anzeigen Ihrer Namespaceressourcen.
- **Workloads** zeigt Informationen zu Bereitstellungen, Pods, Replikatgruppen und Daemonsets an, die in Ihrem Cluster bereitgestellt sind. Der folgende Screenshot zeigt die Standardsystempods in einem AKS-Beispielcluster.
- **Dienste und Eingänge** zeigt alle Dienst- und Eingangsressourcen Ihres Clusters an.

:::image type="content" source="media/kubernetes-portal/workloads.png" alt-text="Im Azure-Portal angezeigte Kubernetes-Podinformationen." lightbox="media/kubernetes-portal/workloads.png":::

### <a name="deploy-an-application"></a>Bereitstellen von Anwendungen

In diesem Beispiel verwenden wir unseren AKS-Beispielcluster zum Bereitstellen der Azure Vote-Anwendung aus dem [AKS-Schnellstart][portal-quickstart].

1. Wählen Sie in einer der Ressourcenansichten („Namespace“, „Workloads“ oder „Dienste und Eingänge“) **Hinzuzufügen** aus.
1. Fügen Sie die YAML-Datei für die Azure Vote-Anwendung aus dem [AKS-Schnellstart][portal-quickstart] ein.
1. Wählen Sie unten im YAML-Editor **Hinzufügen** aus, um die Anwendung bereitzustellen. 

Nachdem die YAML-Datei hinzugefügt wurde, zeigt die Ressourcenanzeige sowohl die Kubernetes-Dienste an, die erstellt wurden, den internen Dienst (azure-vote-back), als auch den externen Dienst (azure-vote-front), um auf die Azure Vote-Anwendung zuzugreifen. Der externe Dienst umfasst eine verknüpfte externe IP-Adresse, sodass Sie die Anwendung bequem in Ihrem Browser anzeigen können.

:::image type="content" source="media/kubernetes-portal/portal-services.png" alt-text="Im Azure-Portal angezeigte Azure Vote-Anwendungsinformationen." lightbox="media/kubernetes-portal/portal-services.png":::

### <a name="monitor-deployment-insights"></a>Überwachen von Bereitstellungserkenntnissen

AKS-Cluster mit aktiviertem [Azure Monitor für Container][enable-monitor] können Bereitstellungserkenntnisse schnell anzeigen. In der Kubernetes-Ressourcenansicht können Benutzer den Livestatus von einzelnen Bereitstellungen anzeigen, einschließlich CPU- und Speicherauslastung, sowie zu Azure Monitor wechseln, um ausführlichere Informationen zu erhalten. Im Folgenden finden Sie ein Beispiel für Bereitstellungserkenntnisse aus einem AKS-Beispielcluster:

:::image type="content" source="media/kubernetes-portal/deployment-insights.png" alt-text="Im Azure-Portal angezeigte Bereitstellungserkenntnisse." lightbox="media/kubernetes-portal/deployment-insights.png":::

## <a name="edit-yaml"></a>Bearbeiten von YAML

Die Kubernetes-Ressourcenansicht enthält auch einen YAML-Editor. Ein integrierter YAML-Editor bedeutet, dass Sie Dienste und Bereitstellungen im Portal aktualisieren oder erstellen und die Änderungen sofort anwenden können.

:::image type="content" source="media/kubernetes-portal/service-editor.png" alt-text="Im Azure-Portal angezeigter YAML-Editor für einen Kubernetes-Dienst.":::

Nachdem Sie die YAML-Datei bearbeitet haben, werden Änderungen angewendet, indem Sie **Überprüfen und speichern** auswählen, die Änderungen bestätigen und dann erneut speichern.

>[!WARNING]
> Es wird nicht empfohlen, direkte Produktionsänderungen über die Benutzeroberfläche oder die CLI vorzunehmen. Sie sollten stattdessen die [bewährten Methoden von Continuous Integration (CI) und Continuous Deployment (CD)](kubernetes-action.md) nutzen. Die Kubernetes-Verwaltungsfunktionen und der YAML-Editor im Azure-Portal sind für das Erlernen und Erstellen von Flights neuer Bereitstellungen in einer Entwicklungs- und Testumgebung konzipiert.

## <a name="troubleshooting"></a>Problembehandlung

In diesem Abschnitt werden einige allgemeine Probleme und Schritte zur Problembehandlung erläutert.

### <a name="unauthorized-access"></a>Nicht autorisierter Zugriff

Für den Zugriff auf die Kubernetes-Ressourcen müssen Sie Zugriff auf den AKS-Cluster, die Kubernetes-API und die Kubernetes-Objekte besitzen. Stellen Sie sicher, dass Sie entweder ein Clusteradministrator oder ein Benutzer mit den geeigneten Berechtigungen für den Zugriff auf den AKS-Cluster sind. Weitere Informationen zur Clustersicherheit finden Sie unter [Zugriffs- und Identitätsoptionen für AKS][concepts-identity].

>[!NOTE]
> Die Ressourcenansicht von Kubernetes im Azure-Portal wird nur von [verwalteten, AAD-fähigen Clustern](managed-aad.md) oder von nicht AAD-fähigen Clustern unterstützt. Wenn Sie einen verwalteten AAD-fähigen Cluster verwenden, muss Ihr AAD-Benutzer oder Ihre Identität über die entsprechenden Rollen/Rollenbindungen verfügen, um auf die Kubernetes-API zugreifen zu können. Außerdem muss die Berechtigung zum Pullen des [Benutzers `kubeconfig`](control-kubeconfig-access.md) gewährt worden sein.

### <a name="enable-resource-view"></a>Aktivieren der Ressourcenansicht

Bei vorhandenen Clustern müssen Sie möglicherweise die Kubernetes-Ressourcenansicht aktivieren. Um die Ressourcenansicht zu aktivieren, befolgen Sie die Anweisungen im Portal für Ihren Cluster.

:::image type="content" source="media/kubernetes-portal/enable-resource-view.png" alt-text="Azure-Portalmeldung für die Aktivierung der Kubernetes-Ressourcenansicht." lightbox="media/kubernetes-portal/enable-resource-view.png":::

> [!TIP]
> Das AKS-Feature für [**vom API-Server autorisierte IP-Adressbereiche**](api-server-authorized-ip-ranges.md) kann hinzugefügt werden, um den Zugriff des API-Servers auf den öffentlichen Endpunkt der Firewall zu beschränken. Eine weitere Option für solche Cluster stellt das Aktualisieren von `--api-server-authorized-ip-ranges` dar, um den Zugriff für einen lokalen Clientcomputer oder einen IP-Adressbereich (aus dem das Portal aufgerufen wird) einzubeziehen. Um diesen Zugriff zuzulassen, benötigen Sie die öffentliche IPv4-Adresse des Computers. Sie finden diese Adresse mithilfe des Befehls unten, oder indem Sie in einem Internetbrowser nach „Wie lautet meine IP-Adresse“ suchen.
```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde gezeigt, wie Sie auf Kubernetes-Ressourcen für Ihren AKS-Cluster zugreifen. Tiefer gehende Informationen zu Clusterressourcen und den YAML-Dateien, auf die mit der Kubernetes-Ressourcenanzeige zugegriffen wird, finden Sie unter [Bereitstellungen und YAML-Manifeste][deployments].

<!-- LINKS - internal -->
[kubernetes-dashboard]: kubernetes-dashboard.md
[concepts-identity]: concepts-identity.md
[portal-quickstart]: kubernetes-walkthrough-portal.md#run-the-application
[deployments]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[aks-managed-aad]: managed-aad.md
[cli-aad-upgrade]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
[enable-monitor]: ../azure-monitor/insights/container-insights-enable-existing-clusters.md