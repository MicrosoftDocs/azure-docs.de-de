---
title: Problembehandlung bei allgemeinen Problemen mit Azure Arc-fähigem Kubernetes (Vorschauversion)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Problembehandlung bei häufigen Problemen mit Arc-fähigen Kubernetes-Clustern
keywords: Kubernetes, Arc, Azure, Container
ms.openlocfilehash: 2e05701b92d2d96074a07e0e61ce65145a1d9dfd
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302379"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting-preview"></a>Problembehandlung bei Azure Arc-fähigem Kubernetes (Vorschauversion)

Dieses Dokument enthält einige häufig auftretende Szenarien zur Behandlung von Problemen bei Konnektivität, Berechtigungen und Agents.

## <a name="general-troubleshooting"></a>Allgemeine Problembehandlung

### <a name="azure-cli-set-up"></a>Einrichten der Azure-Befehlszeilenschnittstelle
Vergewissern Sie sich vor der Verwendung der Befehlszeilenschnittstellen-Befehle „az connectedk8s“ oder „az k8sconfiguration“, dass „az“ für die Verwendung des richtigen Azure-Abonnements eingerichtet ist.

```console
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>Azure Arc-Agents
Alle Agents für Azure Arc-fähiges Kubernetes werden als Pods im `azure-arc`-Namespace bereitgestellt. Im Normalbetrieb sollten alle Pods ausgeführt werden und ihre Integritätsprüfungen bestehen.

Überprüfen Sie zunächst die Azure Arc-Helm-Version:

```console
$ helm --namespace default status azure-arc
NAME: azure-arc
LAST DEPLOYED: Fri Apr  3 11:13:10 2020
NAMESPACE: default
STATUS: deployed
REVISION: 5
TEST SUITE: None
```

Wenn das Helm-Release nicht gefunden wurde oder nicht vorhanden ist, versuchen Sie erneut, ein Onboarding des Clusters durchzuführen.

Wenn das Helm-Release vorhanden ist und den `STATUS: deployed` aufweist, ermitteln Sie den Status des Agents mithilfe von `kubectl`:

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                        READY   UP-TO-DATE AVAILABLE AGE
deployment.apps/cluster-metadata-operator   1/1     1           1        16h
deployment.apps/clusteridentityoperator     1/1     1           1        16h
deployment.apps/config-agent                1/1     1           1        16h
deployment.apps/controller-manager          1/1     1           1        16h
deployment.apps/flux-logs-agent             1/1     1           1        16h
deployment.apps/metrics-agent               1/1     1           1        16h
deployment.apps/resource-sync-agent         1/1     1           1        16h

NAME                                            READY   STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

Für alle Pods sollte `STATUS` den Wert `Running` und `READY` entweder den Wert `3/3` oder `2/2` haben. Rufen Sie die Protokolle ab, und überprüfen Sie, ob für Pods der Wert `Error` oder `CrashLoopBackOff` angegeben ist. Wenn einer dieser Pods mit dem Status `Pending` hängen bleibt, liegt dies möglicherweise daran, dass die Ressourcen auf den Clusterknoten nicht ausreichen. Wenn Sie [Ihren Cluster hochskalieren](https://kubernetes.io/docs/tasks/administer-cluster/), gehen diese Pods in den Status `Running` über.

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>Verbinden von Kubernetes-Clustern mit Azure Arc

Das Herstellen einer Verbindung von Clustern mit Azure erfordert Zugriff auf ein Azure-Abonnement und `cluster-admin`-Zugriff auf einen Zielcluster. Wenn der Cluster nicht erreicht werden kann oder unzureichende Berechtigungen aufweist, tritt beim Onboarding ein Fehler auf.

### <a name="insufficient-cluster-permissions"></a>Unzureichende Clusterberechtigungen

Wenn die bereitgestellte kubeconfig-Datei nicht über ausreichende Berechtigungen zum Installieren von Azure Arc-Agents verfügt, gibt der Azure CLI-Befehl beim Versuch, die Kubernetes-API aufzurufen, einen Fehler zurück.

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

Der Clusterbesitzer sollte einen Kubernetes-Benutzer mit Clusteradministratorberechtigungen verwenden.

### <a name="installation-timeouts"></a>Timeouts bei der Installation

Die Installation des Azure Arc-Agents erfordert die Ausführung einer Gruppe von Containern auf dem Zielcluster. Wenn der Cluster über eine langsame Internetverbindung ausgeführt wird, kann das Abrufen des Containerimages länger dauern, als die Azure CLI-Timeouts erlauben.

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

### <a name="helm-issue"></a>Problem mit Helm

Die Helm-Version `v3.3.0-rc.1` hat ein [Problem](https://github.com/helm/helm/pull/8527). Nach der Helm-Installation bzw. dem Helm-Upgrade (unter der Haube durch die CLI-Erweiterung „connectedk8s“ verwendet) wird durch das Ausführen von allen Hooks der folgende Fehler erzeugt:

```console
$ az connectedk8s connect -n shasbakstest -g shasbakstest
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding.
This operation might take a while...

Please check if the azure-arc namespace was deployed and run 'kubectl get pods -n azure-arc' to check if all the pods are in running state. A possible cause for pods stuck in pending state could be insufficientresources on the kubernetes cluster to onboard to arc.
ValidationError: Unable to install helm release: Error: customresourcedefinitions.apiextensions.k8s.io "connectedclusters.arc.azure.com" not found
```

Befolgen Sie diese Schritte, um das Problem zu beheben:

1. Löschen Sie die betroffene Kubernetes-Ressource mit Azure Arc-Aktivierung im Azure-Portal.
2. Führen Sie die folgenden Befehle auf dem Computer aus:
    
    ```console
    kubectl delete ns azure-arc
    kubectl delete clusterrolebinding azure-arc-operator
    kubectl delete secret sh.helm.release.v1.azure-arc.v1
    ```

3. [Installieren Sie eine stabile Version](https://helm.sh/docs/intro/install/) von Helm 3 auf Ihrem Computer anstelle der Release Candidate-Version.
4. Führen Sie den Befehl `az connectedk8s connect` mit den entsprechenden Werten aus, um den Cluster mit Azure Arc zu verbinden.

## <a name="configuration-management"></a>Konfigurationsverwaltung

### <a name="general"></a>Allgemein
Um Probleme mit der Konfiguration der Quellcodeverwaltung zu beheben, führen Sie az-Befehle mit dem Schalter --debug aus.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8sconfiguration create <parameters> --debug
```

### <a name="create-source-control-configuration"></a>Erstellen der Konfiguration für die Quellcodeverwaltung
Die Rolle „Mitwirkender“ für die Ressource „Microsoft.Kubernetes/connectedCluster“ ist zum Erstellen der Ressource „Microsoft.KubernetesConfiguration/sourceControlConfiguration“ erforderlich und ausreichend.

### <a name="configuration-remains-pending"></a>Konfiguration hat unverändert den Status `Pending`

```console
kubectl -n azure-arc logs -l app.kubernetes.io/component=config-agent -c config-agent
$ k -n pending get gitconfigs.clusterconfig.azure.com  -o yaml
apiVersion: v1
items:
- apiVersion: clusterconfig.azure.com/v1beta1
  kind: GitConfig
  metadata:
    creationTimestamp: "2020-04-13T20:37:25Z"
    generation: 1
    name: pending
    namespace: pending
    resourceVersion: "10088301"
    selfLink: /apis/clusterconfig.azure.com/v1beta1/namespaces/pending/gitconfigs/pending
    uid: d9452407-ff53-4c02-9b5a-51d55e62f704
  spec:
    correlationId: ""
    deleteOperator: false
    enableHelmOperator: false
    giturl: git@github.com:slack/cluster-config.git
    helmOperatorProperties: null
    operatorClientLocation: azurearcfork8s.azurecr.io/arc-preview/fluxctl:0.1.3
    operatorInstanceName: pending
    operatorParams: '"--disable-registry-scanning"'
    operatorScope: cluster
    operatorType: flux
  status:
    configAppliedTime: "2020-04-13T20:38:43.081Z"
    isSyncedWithAzure: true
    lastPolledStatusTime: ""
    message: 'Error: {exit status 1} occurred while doing the operation : {Installing
      the operator} on the config'
    operatorPropertiesHashed: ""
    publicKey: ""
    retryCountPublicKey: 0
    status: Installing the operator
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```
## <a name="monitoring"></a>Überwachung

Azure Monitor für Container erfordert die Ausführung des DaemonSet im privilegierten Modus. Führen Sie den folgenden Befehl aus, um einen Canonical Charmed Kubernetes-Cluster für die Überwachung einzurichten:

```console
juju config kubernetes-worker allow-privileged=true
```