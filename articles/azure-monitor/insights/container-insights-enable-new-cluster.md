---
title: Überwachen eines neuen AKS-Clusters (Azure Kubernetes Service) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie die Überwachung eines neuen AKS-Clusters (Azure Kubernetes Service) mit Azure Monitor für ein Containerabonnement aktivieren.
ms.topic: conceptual
ms.date: 04/25/2019
ms.custom: devx-track-terraform, devx-track-azurecli
ms.openlocfilehash: 19c4a88cee8776136593b041e94dd14c7c9c28d6
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92735073"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Aktivieren der Überwachung eines neuen AKS-Clusters (Azure Kubernetes Service)

In diesem Artikel erfahren Sie, wie Sie Azure Monitor für Container einrichten, um verwaltete, in [Azure Kubernetes Service](../../aks/index.yml) gehostete Kubernetes-Cluster zu überwachen, die Sie für die Bereitstellung in Ihrem Abonnement vorbereiten.

Sie können die Überwachung eines AKS-Clusters mit einer der unterstützten Methoden aktivieren:

* Azure-Befehlszeilenschnittstelle
* Terraform

## <a name="enable-using-azure-cli"></a>Aktivieren mithilfe der Azure-Befehlszeilenschnittstelle

Um die Überwachung eines mit der Azure-Befehlszeilenschnittstelle erstellten neuen AKS-Clusters zu aktivieren, führen Sie die Schritte im Schnellstartartikel im Abschnitt [Erstellen eines AKS-Clusters](../../aks/kubernetes-walkthrough.md#create-aks-cluster) durch.  

>[!NOTE]
>Wenn Sie die Azure CLI verwenden möchten, müssen Sie sie zuerst installieren und lokal verwenden. Sie benötigen Azure CLI 2.0.74 oder höher. Um Ihre Version zu ermitteln, führen Sie `az --version` aus. Informationen zur Installation und zum Upgrade von Azure CLI finden Sie unter [Installieren von Azure CLI](/cli/azure/install-azure-cli). Wenn Sie Version 0.4.12 oder höher der CLI-Erweiterung „aks-preview“ installiert haben, entfernen Sie alle Änderungen, die Sie vorgenommen haben, um eine Vorschauerweiterung zu aktivieren, da diese das Standardverhalten der Azure CLI außer Kraft setzen kann. Der Grund dafür ist, dass AKS-Previewfunktionen in der Azure US Government-Cloud nicht verfügbar sind.

## <a name="enable-using-terraform"></a>Aktivieren mithilfe von Terraform

Wenn Sie [einen neuen AKS-Cluster mit Terraform](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks) bereitstellen, geben Sie die Argumente an, die im Profil [zum Erstellen eines Log Analytics-Arbeitsbereichs](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) erforderlich sind, wenn Sie kein vorhandenes Argument angeben. 

>[!NOTE]
>Wenn Sie sich für Terraform entscheiden, müssen Sie den Terraform Azure RM-Anbieter, Version 1.17.0 oder höher, verwenden.

Informationen zum Hinzufügen von Azure Monitor für Container zum Arbeitsbereich finden Sie unter [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html), und vervollständigen Sie das Profil, indem Sie [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) einbeziehen und **oms_agent** angeben. 

Nachdem Sie die Überwachung aktiviert haben, und alle Konfigurationsaufgaben erfolgreich abgeschlossen wurden, haben Sie zwei Möglichkeiten, die Leistung Ihres Clusters zu überwachen:

* Direkt über das AKS-Cluster durch Auswählen von **Integrität** im linken Bereich.
* Durch Auswählen der Kachel **Monitor Container insights** (Containereinblicke überwachen) auf der AKS-Clusterseite für den ausgewählten Cluster. Wählen Sie in Azure Monitor im linken Bereich **Integrität** aus. 

  ![Optionen zum Auswählen von Azure Monitor für Container in AKS](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

Nach dem Aktivieren der Überwachung kann es ca. 15 Minuten dauern, bis Integritätsmetriken für den Cluster angezeigt werden. 

## <a name="verify-agent-and-solution-deployment"></a>Überprüfen der Agent- und Lösungsbereitstellung
Ab Agent-Version *06072018* können Sie überprüfen, ob Agent und Lösung erfolgreich bereitgestellt wurden. In früheren Agent-Versionen können Sie nur die Agent-Bereitstellung überprüfen.

### <a name="agent-version-06072018-or-later"></a>Agent-Version 06072018 oder höher
Führen Sie den folgenden Befehl aus, um zu überprüfen, ob der Agent erfolgreich bereitgestellt wurde. 

```
kubectl get ds omsagent --namespace=kube-system
```

Die Ausgabe sollte wie folgt aussehen, was auf eine ordnungsgemäße Bereitstellung hinweist:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Um die Bereitstellung der Lösung zu überprüfen, führen Sie den folgenden Befehl aus:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Die Ausgabe sollte wie folgt aussehen, was auf eine ordnungsgemäße Bereitstellung hinweist:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Agent-Version vor 06072018

Um zu überprüfen, ob die Log Analytics-Agent-Version vor *06072018* ordnungsgemäß bereitgestellt wurde, führen Sie den folgenden Befehl aus:  

```
kubectl get ds omsagent --namespace=kube-system
```

Die Ausgabe sollte wie folgt aussehen, was auf eine ordnungsgemäße Bereitstellung hinweist:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Anzeigen der Konfiguration mit der Befehlszeilenschnittstelle
Verwenden Sie den Befehl `aks show`, um Details wie den Aktivierungsstatus der Lösung, die Ressourcen-ID für den Log Analytics-Arbeitsbereich und zusammenfassende Details zum Cluster abzurufen.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Nach wenigen Minuten ist die Ausführung des Befehls abgeschlossen, und es werden Informationen zur Lösung im JSON-Format zurückgegeben.  In den Ergebnissen des Befehls sollte das Profil des Überwachungs-Add-Ons angezeigt werden, ähnlich der folgenden Beispielausgabe:

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>Nächste Schritte

* Wenn beim Onboarding der Lösung Probleme auftreten, lesen Sie den [Leitfaden zur Problembehandlung](container-insights-troubleshoot.md)

* Wenn die Überwachung aktiviert ist, um Integrität und Ressourcennutzung Ihres AKS-Clusters und der darauf ausgeführten Workloads zu erfassen, informieren Sie sich über die [Verwendung](container-insights-analyze.md) von Azure Monitor für Container.

