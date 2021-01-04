---
title: Verwenden der Autoskalierung für Cluster in Azure Kubernetes Service (AKS)
description: In diesem Artikel erfahren Sie, wie Sie Ihren Cluster mithilfe der Autoskalierung automatisch zur Erfüllung der Anwendungsanforderungen in einem Azure Kubernetes Service-Cluster (AKS) skalieren.
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: e644a931152c83a5232c8233d519f7807ab708af
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542640"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Automatisches Skalieren eines Clusters zur Erfüllung von Anwendungsanforderungen in Azure Kubernetes Service (AKS)

Um die Anwendungsanforderungen in Azure Kubernetes Service (AKS) zu erfüllen, müssen Sie möglicherweise die Anzahl von Knoten anpassen, die Ihre Workloads ausführen. Die Komponente für die automatische Clusterskalierung kann auf Pods in Ihrem Cluster überprüfen, die aufgrund von Ressourceneinschränkungen nicht geplant werden können. Sobald Probleme erkannt werden, wird die Anzahl von Knoten in einem Knotenpool erhöht, um den Anwendungsanforderungen gerecht zu werden. Die Knoten werden außerdem regelmäßig auf einen Mangel an ausgeführten Pods überprüft, und bei Bedarf wird die Anzahl von Knoten verringert. Diese Fähigkeit zum automatischen Hoch- oder Herunterskalieren der Knotenanzahl in Ihrem AKS-Cluster ermöglicht es Ihnen, einen effizienten, kostengünstigen Cluster zu betreiben.

In diesem Artikel wird gezeigt, wie Sie die Autoskalierung für Cluster in einem AKS-Cluster aktivieren und verwalten.

## <a name="before-you-begin"></a>Voraussetzungen

Der Artikel setzt voraus, dass Sie mindestens Version 2.0.76 der Azure-Befehlszeilenschnittstelle (Azure CLI) ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli-install].

## <a name="about-the-cluster-autoscaler"></a>Grundlegendes zur Autoskalierung für Cluster

Für eine Anpassung an sich ändernde Anwendungsanforderungen, z.B. zwischen Arbeitstag und Abend oder an einem Wochenende, benötigen Cluster oft eine Möglichkeit zur automatischen Skalierung. AKS-Cluster können auf zwei Arten skaliert werden:

* Die **Autoskalierung für Cluster** überprüft auf Pods, die aufgrund von Ressourceneinschränkungen nicht auf Knoten geplant werden können. Der Cluster erhöht in diesem Fall automatisch die Anzahl von Knoten.
* Bei der **horizontalen automatischen Podskalierung** überwacht der Metrikserver in einem Kubernetes-Cluster die Ressourcenanforderungen der Pods. Wenn eine Anwendung mehr Ressourcen benötigt, wird die Anzahl von Pods automatisch erhöht, um den Bedarf zu decken.

![Die Autoskalierung für Cluster und die horizontale automatische Podskalierung arbeiten häufig zusammen, um die Anwendungsanforderungen zu erfüllen.](media/autoscaler/cluster-autoscaler.png)

Sowohl über die horizontale automatische Podskalierung als auch über die Autoskalierung für Cluster kann bei Bedarf die Anzahl von Pods und Knoten verringert werden. Die Autoskalierung für Cluster verringert die Anzahl von Knoten, wenn für einen längeren Zeitraum Kapazität ungenutzt bleibt. Pods auf einem Knoten, der durch die Autoskalierung für Cluster entfernt werden soll, werden sicher an anderer Stelle im Cluster geplant. Die Autoskalierung für Cluster kann Pods möglicherweise nicht zentral herunterskalieren, wenn ein Verschieben nicht möglich ist. Dies kann in folgenden Situationen der Fall sein:

* Ein Pod wird direkt erstellt und nicht von einem Controllerobjekt wie z. B. einer Bereitstellungs- oder Replikatgruppe unterstützt.
* Ein Budget für die Unterbrechung von Pods (Pod Disruption Budget, PDB) ist zu restriktiv und lässt nicht zu, dass die Anzahl von Pods unter einen bestimmten Schwellenwert fällt.
* Ein Pod verwendet Knotenselektoren oder Anti-Affinität, die nicht berücksichtigt werden können, wenn der Pod auf einem anderen Knoten geplant wird.

Weitere Informationen dazu, warum ein zentrales Herunterskalieren über die Autoskalierung für Cluster möglicherweise nicht möglich ist, finden Sie unter [What types of pods can prevent the cluster autoscaler from removing a node?][autoscaler-scaledown] (Welche Podtypen können das Entfernen eines Knotens durch die Autoskalierung für Cluster verhindern?).

Die Autoskalierung für Cluster verwendet Startparameter, um z.B. Zeitintervalle zwischen Skalierungsereignissen und Ressourcenschwellenwerte zu steuern. Weitere Informationen zu den Parametern, die von der Autoskalierung für Cluster verwendet werden, finden Sie unter [Verwenden des Autoskalierungsprofils](#using-the-autoscaler-profile).

Die automatische Clusterskalierung und die horizontale automatische Podskalierung können zusammenarbeiten und werden häufig beide in einem Cluster bereitgestellt. In Kombination konzentriert sich die horizontale automatische Podskalierung auf die Anzahl von Pods, die zum Erfüllen der Anwendungsanforderungen benötigt werden. Die Autoskalierung für Cluster konzentriert sich auf die Anzahl von Knoten, die zum Unterstützen der geplanten Pods erforderlich sind.

> [!NOTE]
> Bei Verwendung der Autoskalierung für Cluster ist die manuelle Skalierung deaktiviert. Überlassen Sie der Autoskalierung für Cluster das Bestimmen der erforderlichen Anzahl von Knoten. Wenn Sie Ihren Cluster manuell skalieren möchten, [deaktivieren Sie die Autoskalierung für Cluster](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Erstellen eines AKS-Clusters und Aktivieren der Autoskalierung für Cluster

Wenn Sie einen AKS-Cluster erstellen müssen, verwenden Sie den Befehl [az aks create][az-aks-create]. Um die automatische Clusterskalierung im Knotenpool für den Cluster zu aktivieren und zu konfigurieren, verwenden Sie den Parameter `--enable-cluster-autoscaler` und geben einen `--min-count`- und einen `--max-count`-Wert für die Knoten an.

> [!IMPORTANT]
> Bei der automatischen Clusterskalierungsfunktion handelt es sich um eine Kubernetes-Komponente. Obwohl der AKS-Cluster eine VM-Skalierungsgruppe für die Knoten verwendet, sollten Sie die Einstellungen für die automatische Skalierung für die Skalierungsgruppe im Azure-Portal oder über die Azure CLI nicht manuell aktivieren oder bearbeiten. Lassen Sie die automatische Skalierungsfunktion für den Kubernetes-Cluster die erforderlichen Skalierungseinstellungen verwalten. Weitere Informationen finden Sie unter [Kann ich die AKS-Ressourcen in der Knotenressourcengruppe ändern?][aks-faq-node-resource-group]

Im folgenden Beispiel wird ein AKS-Cluster mit einem einzigen Knotenpool erstellt, der von einer VM-Skalierungsgruppe unterstützt wird. Außerdem wird die automatische Clusterskalierung im Knotenpool für den Cluster aktiviert und die Mindestanzahl von Knoten auf *1* sowie die maximale Knotenanzahl auf *3* festgelegt:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Die Erstellung des Clusters und die Konfiguration der Einstellungen für die Autoskalierung für Cluster dauert einige Minuten.

## <a name="update-an-existing-aks-cluster-to-enable-the-cluster-autoscaler"></a>Aktualisieren eines vorhandenen AKS-Clusters zur Aktivierung der Autoskalierung für Cluster

Verwenden Sie den Befehl [az aks update][az-aks-update], um die Autoskalierung für den vorhandenen Cluster im Knotenpool zu aktivieren und zu konfigurieren. Verwenden Sie den Parameter `--enable-cluster-autoscaler`, und geben Sie einen `--min-count`- und einen `--max-count`-Wert für die Knoten an.

> [!IMPORTANT]
> Bei der automatischen Clusterskalierungsfunktion handelt es sich um eine Kubernetes-Komponente. Obwohl der AKS-Cluster eine VM-Skalierungsgruppe für die Knoten verwendet, sollten Sie die Einstellungen für die automatische Skalierung für die Skalierungsgruppe im Azure-Portal oder über die Azure CLI nicht manuell aktivieren oder bearbeiten. Lassen Sie die automatische Skalierungsfunktion für den Kubernetes-Cluster die erforderlichen Skalierungseinstellungen verwalten. Weitere Informationen finden Sie unter [Kann ich die AKS-Ressourcen in der Knotenressourcengruppe ändern?][aks-faq-node-resource-group]

Das folgende Beispiel aktualisiert einen vorhandenen AKS-Cluster, um die Autoskalierung für Cluster im Knotenpool für den Cluster zu aktivieren, und legt Mindestanzahl von Knoten auf *1* sowie die maximale Knotenanzahl auf *3* fest:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Die Aktualisierung des Clusters und die Konfiguration der Einstellungen für die Autoskalierung für Cluster dauert einige Minuten.

## <a name="change-the-cluster-autoscaler-settings"></a>Ändern der Einstellungen zur Autoskalierung für Cluster

> [!IMPORTANT]
> Wenn Sie über mehrere Knotenpools in Ihrem AKS-Cluster verfügen, fahren Sie mit dem Abschnitt [Automatische Skalierung mit mehreren Agent-Pools](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled) fort. Für Cluster mit mehreren Agent-Pools ist die Verwendung des `az aks nodepool`-Befehlssatzes anstelle von `az aks` erforderlich, um spezifische Eigenschaften von Knotenpools zu ändern.

Im vorherigen Schritt zum Erstellen eines AKS-Clusters oder Aktualisieren eines vorhandenen Knotenpools wurde die Mindestanzahl von Knoten zur automatischen Clusterskalierung auf *1* und die maximale Knotenanzahl auf *3* festgelegt. Wenn sich die Anforderungen Ihrer Anwendung ändern, müssen Sie möglicherweise die Knotenanzahl für die Autoskalierung für Cluster anpassen.

Verwenden Sie den Befehl [az aks update][az-aks-update], um die Knotenanzahl zu ändern.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Im oben stehenden Beispiel wird die automatische Clusterskalierung für den einzelnen Knotenpool in *myAKSCluster* auf mindestens *1* und höchstens *5* Knoten aktualisiert.

> [!NOTE]
> Die automatische Clusterskalierung trifft Skalierungsentscheidungen auf Basis der für die einzelnen Knotenpools festgelegten Mindest- und Höchstzahlen, erzwingt sie jedoch nicht, wenn die Mindest- und Höchstzahlen aktualisiert wurden. Beispielsweise wird der Pool bei Festlegung einer Mindestanzahl von 5 bei einer aktuellen Knotenanzahl von 3 nicht sofort auf 5 skaliert. Wenn Sie die minimale Anzahl für den Knotenpool auf einen höheren Wert als die aktuelle Anzahl von Knoten festlegen, wird dieser neue Minimal- oder Maximalwert berücksichtigt, wenn genügend nicht planbare Pods vorhanden sind, die zwei neue zusätzliche Knoten erfordern und eine automatische Skalierung auslösen würden. Nach dem Skalierungsereignis werden die neuen Anzahlgrenzwerte berücksichtigt.

Überwachen Sie die Leistung Ihrer Anwendungen und Dienste, und passen Sie die Anzahl von Knoten zur Autoskalierung für Cluster an die erforderliche Leistung an.

## <a name="using-the-autoscaler-profile"></a>Verwenden des Autoskalierungsprofils

Sie können auch differenziertere Details der Clusterautoskalierung konfigurieren, indem Sie die Standardwerte im clusterweiten Autoskalierungsprofil ändern. Ein Ereignis zum zentralen Herunterskalieren tritt z. B. ein, wenn die Knoten nach 10 Minuten nicht ausgelastet sind. Wenn Sie Workloads hatten, die alle 15 Minuten ausgeführt wurden, möchten Sie vielleicht das Autoskalierungsprofil so ändern, dass es nach 15 oder 20 Minuten nicht ausgelastete Knoten herunterskaliert. Wenn Sie die Clusterautoskalierung aktivieren, wird ein Standardprofil verwendet, sofern Sie keine anderen Einstellungen angeben. Das Profil für die Clusterautoskalierung verfügt über die folgenden Einstellungen, die Sie aktualisieren können:

| Einstellung                          | BESCHREIBUNG                                                                              | Standardwert |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| scan-interval                    | Häufigkeit der Neubewertung von Clustern für das Hoch- oder Herunterskalieren                                    | 10 Sekunden    |
| scale-down-delay-after-add       | Wie lange nach dem Hochskalieren die Auswertung für das Herunterskalieren fortgesetzt wird                               | 10 Minuten    |
| scale-down-delay-after-delete    | Wie lange nach dem Löschen des Knotens die Auswertung für das zentrale Herunterskalieren fortgesetzt wird                          | scan-interval |
| scale-down-delay-after-failure   | Wie lange nach dem Fehler beim zentralen Herunterskalieren die Auswertung für das zentrale Herunterskalieren fortgesetzt wird                     | 3 Minuten     |
| scale-down-unneeded-time         | Wie lange ein Knoten nicht benötigt werden sollte, bevor er für das zentrale Herunterskalieren geeignet ist                  | 10 Minuten    |
| scale-down-unready-time          | Wie lange ein nicht bereiter Knoten nicht benötigt werden sollte, bevor er für das zentrale Herunterskalieren geeignet ist         | 20 Minuten    |
| scale-down-utilization-threshold | Auslastungsgrad des Knotens, definiert als Summe der angeforderten Ressourcen dividiert durch die Kapazität, unterhalb derer ein Knoten für das zentrale Herunterskalieren in Betracht gezogen werden kann | 0.5 |
| max-graceful-termination-sec     | Maximale Anzahl von Sekunden, die die Clusterautoskalierung beim Versuch, einen Knoten herunterzuskalieren, auf die Beendigung des Pods wartet | 600 Sekunden   |
| balance-similar-node-groups      | Erkennt ähnliche Knotenpools und gleicht die Anzahl der Knoten zwischen ihnen aus                 | false         |
| Erweiterung                         | Der Typ der Knotenpool[erweiterung](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-expanders), die bei der zentralen Hochskalierung zu verwenden ist. Mögliche Werte: `most-pods`, `random`, `least-waste` | random | 
| skip-nodes-with-local-storage    | Wenn „true“, löscht die Cluster-Autoskalierung nie Knoten mit Pods mit lokalem Speicher, z. B. „EmptyDir“ oder „HostPath“. | true |
| skip-nodes-with-system-pods      | Wenn „true“, löscht die Cluster-Autoskalierung nie Knoten mit Pods aus kube-system (außer bei „DaemonSet“ oder Spiegelpods). | true | 
| max-empty-bulk-delete            | Maximale Anzahl leerer Knoten, die gleichzeitig gelöscht werden können.                      | 10 Knoten      |
| new-pod-scale-up-delay           | Für Szenarien wie Burst-/Batchskalierung, bei denen die Zertifizierungsstelle nicht agieren soll, bevor der Kubernetes-Scheduler alle Pods planen konnte, können Sie die Zertifizierungsstelle anweisen, nicht geplante Pods zu ignorieren, bevor sie ein bestimmtes Alter erreicht haben.                                                                                                                | 10 Sekunden    |
| max-total-unready-percentage     | Maximaler Prozentsatz der nicht fertigen Knoten im Cluster. Wenn dieser Prozentsatz überschritten wird, hält die Zertifizierungsstelle alle Vorgänge an. | 45 % | 
| ok-total-unready-count           | Anzahl der zulässigen nicht fertigen Knoten, unabhängig von „max-total-unready-percentage“.            | 3 Knoten       |

> [!IMPORTANT]
> Das Profil der Clusterautoskalierung betrifft alle Knotenpools, die die Clusterautoskalierung verwenden. Sie können kein Autoskalierungsprofil pro Knotenpool festlegen.
>
> Für das Clusterautoskalierungsprofil ist die Azure CLI-Version  *2.11.1* oder höher erforderlich. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli-install].

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>Festlegen des Profils für die Clusterautoskalierung in einem vorhandenen AKS-Cluster

Verwenden Sie den Befehl [az aks update][az-aks-update-preview] mit dem Parameter *cluster-autoscaler-profile* , um das Clusterautoskalierungsprofil für Ihren Cluster festzulegen. Das folgende Beispiel konfiguriert die Einstellung des Scanintervalls im Profil auf 30 Sekunden.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

Wenn Sie die Clusterautoskalierung für Knotenpools im Cluster aktivieren, verwenden diese Cluster auch das Profil der Clusterautoskalierung. Beispiel:

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!IMPORTANT]
> Wenn Sie das Profil für die Clusterautoskalierung festlegen, werden alle vorhandenen Knotenpools mit aktivierter Clusterautoskalierung sofort mit der Verwendung des Profils beginnen.

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>Festlegen des Profils für die Clusterautoskalierung beim Erstellen eines AKS-Clusters

Sie können auch den Parameter *cluster-autoscaler-profile* verwenden, wenn Sie Ihren Cluster erstellen. Beispiel:

```azurecli-interactive
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3 \
  --cluster-autoscaler-profile scan-interval=30s
```

Der obige Befehl erstellt einen AKS-Cluster und legt für das Scanintervall 30 Sekunden für das clusterweite Autoskalierungsprofil fest. Der Befehl aktiviert auch die Clusterautoskalierung des ursprünglichen Knotenpools, legt die minimale Knotenanzahl auf 1 und die maximale Knotenanzahl auf 3 fest.

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>Zurücksetzen des Profils für die Clusterautoskalierung auf die Standardwerte

Verwenden Sie den Befehl [az aks update][az-aks-update-preview], um das Profil für die Clusterautoskalierung auf Ihrem Cluster zurückzusetzen.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>Deaktivieren der Autoskalierung für Cluster

Wenn Sie die automatische Clusterskalierung nicht mehr verwenden möchten, können Sie sie mit dem Befehl [az aks update][az-aks-update-preview] unter Angabe des Parameters `--disable-cluster-autoscaler` deaktivieren. Beim Deaktivieren der Autoskalierung für Cluster werden keine Knoten entfernt.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

Nachdem Sie die automatische Clusterskalierung deaktiviert haben, können Sie den Cluster mit dem Befehl [az aks scale][az-aks-scale] manuell skalieren. Wenn Sie die horizontale automatische Podskalierung verwenden, wird dieses Feature nach dem Deaktivieren der Autoskalierung für Cluster weiterhin ausgeführt, aber Pods können möglicherweise nicht geplant werden, wenn alle Knotenressourcen verwendet werden.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Erneutes Aktivieren der deaktivierten automatischen Clusterskalierung

Wenn Sie die automatische Clusterskalierung für einen vorhandenen Cluster wieder aktivieren möchten, können Sie sie mit dem Befehl [az aks update][az-aks-update-preview] unter Angabe des Parameter `--enable-cluster-autoscaler`, `--min-count` und `--max-count` wieder aktivieren.

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>Abrufen von Protokollen und Status der automatischen Clusterskalierung

Zum Diagnostizieren und Debuggen von Ereignissen der automatischen Skalierung können Protokolle und Status aus dem AutoScaler-Add-On abgerufen werden.

AKS verwaltet die automatische Clusterskalierung in Ihrem Namen und führt sie in der verwalteten Steuerungsebene aus. Sie können den Knoten auf Steuerungsebene aktivieren, um die Protokolle und Vorgänge der Zertifizierungsstelle anzuzeigen.

Führen Sie die folgenden Schritte aus, um zu konfigurieren, dass Protokolle per Push aus der automatischen Clusterskalierung in Log Analytics übertragen werden.

1. Richten Sie eine Regel für Ressourcenprotokolle ein, um Protokolle der automatischen Clusterskalierung per Push in Log Analytics zu übertragen. [Die Anweisungen werden hier erläutert][aks-view-master-logs]. Stellen Sie sicher, dass Sie das Kontrollkästchen für `cluster-autoscaler` aktivieren, wenn Sie Optionen für „Protokolle“ auswählen.
1. Wählen Sie auf Ihrem Cluster im Azure-Portal den Abschnitt „Protokolle“ aus.
1. Geben Sie die folgende Beispielabfrage in Log Analytics ein:

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

Sofern Protokolle zum Abrufen vorhanden sind, sollten diese in etwa dem folgenden Beispiel entsprechen.

![Log Analytics-Protokolle](media/autoscaler/autoscaler-logs.png)

Die Cluster-Autoskalierung schreibt auch den Integritätsstatus in eine `configmap` namens `cluster-autoscaler-status`. Führen Sie den folgenden `kubectl`-Befehl aus, um diese Protokolle abzurufen. Für jeden mit der automatischen Clusterskalierung konfigurierten Knotenpool wird ein Integritätsstatus angezeigt.

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

Weitere Informationen dazu, welche Elemente von AutoScaler protokolliert werden, finden Sie in den häufig gestellten Fragen zum [Kubernetes/AutoScaler GitHub-Projekt][kubernetes-faq].

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>Verwenden der automatischen Clusterskalierung mit mehreren aktivierten Knotenpools

Die automatische Clusterskalierung kann zusammen mit aktivierter [Funktion für mehrere Knotenpools][aks-multiple-node-pools] verwendet werden. In diesem Dokument erfahren Sie, wie Sie mehrere Knotenpools aktivieren und einem vorhandenen Cluster zusätzliche Knotenpools hinzufügen. Wenn Sie beide Features gleichzeitig verwenden, aktivieren Sie die automatische Clusterskalierung für jeden einzelnen Knotenpool im Cluster und können jeweils eindeutige Regeln für die automatische Skalierung übergeben.

Der folgende Befehl setzt voraus, dass Sie die [ersten Anweisungen](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) in diesem Dokument befolgt haben und für einen vorhandenen Knotenpool die maximale Anzahl von *3* auf *5* aktualisieren möchten. Verwenden Sie den Befehl [az aks nodepool update][az-aks-nodepool-update], um die Einstellungen eines vorhandenen Knotenpools zu aktualisieren.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Die automatische Clusterskalierung kann mit [az aks nodepool update][az-aks-nodepool-update] unter Angabe des Parameters `--disable-cluster-autoscaler` deaktiviert werden.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

Wenn Sie die automatische Clusterskalierung für einen vorhandenen Cluster wieder aktivieren möchten, können Sie sie mit dem Befehl [az aks nodepool update][az-aks-nodepool-update] unter Angabe der Parameter `--enable-cluster-autoscaler`, `--min-count` und `--max-count` wieder aktivieren.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde gezeigt, wie Sie die Anzahl von AKS-Knoten automatisch skalieren können. Außerdem können Sie mit der horizontalen automatischen Podskalierung automatisch die Anzahl von Pods anpassen, auf denen Ihre Anwendung ausgeführt wird. Die Schritte zum Verwenden der horizontalen automatischen Podskalierung finden Sie unter [Skalieren von Anwendungen in Azure Kubernetes Service (AKS)][aks-scale-apps].

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-master-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why
