---
title: Business Continuity & Disaster Recovery
services: azure-dev-spaces
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 01/28/2019
ms.topic: conceptual
description: Erfahren Sie, wie Sie Azure Dev Spaces und Azure Kubernetes Services verwenden, um Geschäftskontinuität bereitzustellen und die Notfallwiederherstellung vorzubereiten.
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Container, Helm, Service Mesh, Service Mesh-Routing, kubectl, k8s '
manager: gwallace
ms.openlocfilehash: 77425d294dcea0546259b152b793cec291940c53
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929598"
---
# <a name="business-continuity-and-disaster-recovery-in-azure-dev-spaces"></a>Geschäftskontinuität und Notfallwiederherstellung in Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

## <a name="review-disaster-recovery-guidance-for-azure-kubernetes-service-aks"></a>Überprüfen der Anweisungen zur Notfallwiederherstellung für Azure Kubernetes Service (AKS)

Azure Dev Spaces ist ein Feature von Azure Kubernetes Service (AKS). Sie sollten sich mit den Richtlinien für die Notfallwiederherstellung in AKS vertraut machen und überprüfen, ob sie für die AKS-Cluster gelten, die Sie für Dev Spaces verwenden. Weitere Informationen finden Sie in den [Best Practices für Geschäftskontinuität und Notfallwiederherstellung in Azure Kubernetes Service (AKS)](../../aks/operator-best-practices-multi-region.md).

## <a name="enable-dev-spaces-on-aks-clusters-in-different-regions"></a>Aktivieren von Dev Spaces für AKS-Cluster in verschiedenen Regionen

Durch das Aktivieren von Dev Spaces für AKS-Cluster in verschiedenen Regionen können Sie Dev Spaces sofort nach dem Ausfall einer Azure-Region fortsetzen.

Allgemeine Informationen zur Bereitstellung von AKS in mehreren Regionen finden Sie unter [Planen der Bereitstellung in mehreren Regionen](../../aks/operator-best-practices-multi-region.md#plan-for-multiregion-deployment).

### <a name="enable-dev-spaces-via-the-azure-cli"></a>Aktivieren von Dev Spaces über die Azure CLI

Sie können Dev Spaces auch über die Befehlszeile aktivieren:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

## <a name="deploy-your-teams-baseline-to-each-cluster"></a>Bereitstellen einer Teambaseline für jeden Cluster

Bei der Arbeit mit Dev Spaces stellen Sie typischerweise die gesamte Anwendung für einen übergeordneten Entwicklerbereich in Ihrem Kubernetes-Cluster bereit. Standardmäßig wird der Bereich `default` verwendet. Die erste Bereitstellung umfasst alle Dienste sowie die externen Ressourcen, von denen diese Dienste abhängen, z.B. Datenbanken oder Warteschlangen. Diese Bereitstellung wird als *Baseline* bezeichnet. Nachdem Sie eine Baseline im übergeordneten Entwicklungsbereich eingerichtet haben, durchlaufen und debuggen Sie die einzelnen Dienste innerhalb der untergeordneten Entwicklungsbereiche.

Sie müssen die neuesten Versionen Ihrer Baselinedienste für die Cluster in mehreren Regionen bereitstellen. Durch eine Aktualisierung Ihrer Baselinedienste in dieser Weise wird sichergestellt, dass Sie Dev Spaces weiterhin verwenden können, wenn eine Azure-Region ausfällt. Wenn Sie Ihre Baseline beispielsweise per CI/CD-Pipeline bereitstellen, ändern Sie die Pipeline so ab, dass die Bereitstellung in mehreren Clustern in unterschiedlichen Regionen erfolgt.

## <a name="select-the-correct-aks-cluster-to-use-for-dev-spaces"></a>Auswählen des richtigen AKS-Clusters für Dev Spaces

Nach dem erfolgreichen Konfigurieren eines Sicherungsclusters mit Ausführung Ihrer Teambaseline können Sie jederzeit schnell auf den Sicherungscluster umstellen. Anschließend können Sie die einzelnen Dienste, mit denen Sie in untergeordneten Dev Spaces arbeiten, erneut ausführen.

Wählen Sie mit dem folgenden CLI-Befehl einen anderen Cluster aus:

```azurecli
az aks use-dev-spaces -g <new resource group name> -n <new cluster name>
```

Sie können mit dem folgenden Befehl die verfügbaren Entwicklungsbereiche für den neuen Cluster auflisten:

```cmd
azds space list
```

Mit diesem Befehl können Sie einen neuen Entwicklungsbereich erstellen oder einen vorhandenen Entwicklungsbereich auswählen:

```cmd
azds space select -n <space name>
```

Nach Ausführung dieser Befehle werden der ausgewählte Cluster und der Entwicklungsbereich für nachfolgende CLI-Vorgänge und für Debuggingprojekte unter Verwendung der Visual Studio Code-Erweiterung für Azure Dev Spaces verwendet.

Wenn Sie Visual Studio verwenden, können Sie den für ein vorhandenes Projekt verwendeten Cluster über die folgenden Schritte wechseln:

1. Öffnen Sie Ihr Projekt in Visual Studio.
1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen, und klicken Sie dann auf **Eigenschaften**.
1. Klicken Sie im linken Bereich auf **Debuggen**.
1. Klicken Sie auf der Eigenschaftenseite für das Debuggen auf die Dropdownliste **Profil**, und wählen Sie **Azure Dev Spaces** aus.
1. Klicken Sie auf die Schaltfläche **Ändern**.
1. Wählen Sie im angezeigten Dialogfeld den AKS-Cluster aus, den Sie verwenden möchten. Falls gewünscht, wählen Sie einen anderen Entwicklungsbereich für die Arbeit aus, oder erstellen Sie einen neuen Entwicklungsbereich, indem Sie in der Dropdownliste **Bereich** die geeignete Option auswählen.

Nachdem Sie den richtigen Cluster und Bereich ausgewählt haben, können Sie auf F5 drücken, um den Dienst in Dev Spaces auszuführen.

Wiederholen Sie diese Schritte für alle weiteren Projekte, die zur Verwendung des ursprünglichen Clusters konfiguriert sind.

## <a name="access-a-service-on-a-backup-cluster"></a>Zugriff auf einen Dienst für einen Sicherungscluster

Wenn Sie Ihren Dienst zur Verwendung eines öffentlichen DNS-Namens konfiguriert haben, weist der Dienst eine andere URL auf, wenn Sie ihn in einem Sicherungscluster ausführen. Öffentliche DNS-Namen weisen immer das Format `<space name>.s.<root space name>.<service name>.<cluster GUID>.<region>.azds.io` auf. Wenn Sie zu einem anderen Cluster wechseln, ändert sich die Cluster-GUID und möglicherweise die Region.

Dev Spaces zeigt bei Ausführung von `azds up` oder im Visual Studio-Ausgabefenster unter **Azure Dev Spaces** immer die richtige URL für den Dienst an.

Sie können die URL auch abrufen, indem Sie den Befehl `azds list-uris` ausführen:
```
$ azds list-uris
Uri                                                     Status
------------------------------------------------------  ---------
http://default.mywebapi.d05afe7e006a4fddb73c.eus.azds.io/  Available
```

Verwenden Sie diese URL beim Zugriff auf den Dienst.
