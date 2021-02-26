---
title: Herstellen einer Verbindung für einen Azure Arc-fähigen Kubernetes-Cluster (Vorschau)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Stellen Sie eine Verbindung zwischen einem Azure Arc-fähigen Kubernetes-Cluster und Azure Arc her.
keywords: Kubernetes, Arc, Azure, K8s, Container
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: b4ab84153eaaf81c668d8589fec7516853aca5f9
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008110"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Herstellen einer Verbindung für einen Azure Arc-fähigen Kubernetes-Cluster (Vorschau)

In diesem Artikel wird das Herstellen einer Verbindung mit einem von Cloud Native Computing Foundation (CNFC) zertifizierten Kubernetes-Cluster erläutert, z. B. AKS-Engine in Azure oder Azure Stack Hub oder GKE-, EKS- und VMware vSphere-Cluster in Azure Arc.

## <a name="before-you-begin"></a>Voraussetzungen

Prüfen Sie, ob die folgenden Voraussetzungen erfüllt sind:

* Ein funktionierender Kubernetes-Cluster. Wenn Sie über keinen Kubernetes-Cluster verfügen, können Sie eine der folgenden Anleitungen verwenden, um einen Testcluster zu erstellen:
  * Erstellen eines Kubernetes-Clusters mithilfe von [Kubernetes in Docker (kind)](https://kind.sigs.k8s.io/)
  * Erstellen eines Kubernetes-Clusters mithilfe von Docker für [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) oder [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)
* Eine KUBECONFIG-Datei für den Zugriff auf den Cluster und die Rolle des Clusteradministrators im Cluster für die Bereitstellung von Kubernetes-Agents mit Arc-Unterstützung.
* Der für die Befehle `az login` und `az connectedk8s connect` verwendete Benutzer oder Dienstprinzipal muss über die Berechtigungen „Lesen“ und „Schreiben“ für den Ressourcentyp „Microsoft.Kubernetes/connectedclusters“ verfügen. Die Rolle „Kubernetes-Cluster – Azure Arc-Onboarding“ verfügt über diese Berechtigungen und kann für Rollenzuweisungen für den Benutzer oder Dientsprinzipal verwendet werden.
* Helm 3 ist für das Onboarding des Clusters mithilfe der Erweiterung „connectedk8s“ erforderlich. [Installieren Sie die neueste Version von Helm 3](https://helm.sh/docs/intro/install), um diese Anforderung zu erfüllen.
* Azure CLI ab Version 2.15 ist zum Installieren der CLI-Erweiterungen für Kubernetes mit Azure Arc-Unterstützung erforderlich. Installieren Sie die [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true), oder aktualisieren Sie auf die neueste Version.
* Installieren Sie die CLI-Erweiterungen für Kubernetes mit Arc-Unterstützung:
  
  * Installieren Sie die Erweiterung `connectedk8s`, mit der Sie Kubernetes-Cluster mit Azure verbinden können:
  
  ```azurecli
  az extension add --name connectedk8s
  ```
  
   * Installieren Sie die Erweiterung `k8sconfiguration`:
  
  ```azurecli
  az extension add --name k8sconfiguration
  ```

  * Wenn Sie diese Erweiterungen später aktualisieren möchten, führen Sie die folgenden Befehle aus:
  
  ```azurecli
  az extension update --name connectedk8s
  az extension update --name k8sconfiguration
  ```

## <a name="supported-regions"></a>Unterstützte Regionen

* East US
* Europa, Westen

## <a name="network-requirements"></a>Netzwerkanforderungen

Azure Arc-Agents müssen über die folgenden Protokolle/Ports/ausgehenden URLs verfügen, um zu funktionieren:

* TCP an Port 443: `https://:443`
* TCP an Port 9418: `git://:9418`

| Endpunkt (DNS)                                                                                               | BESCHREIBUNG                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Erforderlich, damit der Agent eine Verbindung mit Azure herstellen und den Cluster registrieren kann.                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Endpunkt auf Datenebene, über den der Agent Statusinformationen mithilfe von Push übermitteln und Konfigurationsinformationen abrufen kann                                      |
| `https://login.microsoftonline.com`                                                                            | Erforderlich zum Abrufen und Aktualisieren von Azure Resource Manager-Token.                                                                                    |
| `https://mcr.microsoft.com`                                                                            | Erforderlich zum Pullen von Containerimages für Azure Arc-Agents.                                                                  |
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`                                                                            |  Erforderlich zum Pullen vom System zugewiesener Zertifikate für verwaltete Identitäten.                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Registrieren der beiden Anbieter für Kubernetes mit Azure Arc-Unterstützung:

```console
az provider register --namespace Microsoft.Kubernetes

az provider register --namespace Microsoft.KubernetesConfiguration
```

Die Registrierung ist ein asynchroner Vorgang, der etwa 10 Minuten dauern kann. Sie können den Registrierungsprozess mit den folgenden Befehlen überwachen:

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Verwenden Sie eine Ressourcengruppe zum Speichern von Metadaten für Ihren Cluster.

Erstellen Sie zuerst eine Ressourcengruppe, die die verbundene Clusterressource enthalten soll.

```console
az group create --name AzureArcTest -l EastUS -o table
```

**Ausgabe:**

```console
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-a-cluster"></a>Herstellen einer Verbindung mit einem Cluster

Als Nächstes verbinden Sie Ihren Kubernetes-Cluster mithilfe von `az connectedk8s connect` mit Azure:

1. Überprüfen Sie die Verbindung mit Ihrem Kubernetes-Cluster mithilfe einer der folgenden Möglichkeiten:
   1. `KUBECONFIG`
   1. `~/.kube/config`
   1. `--kube-config`
1. Stellen Sie mit Helm 3 Azure Arc-Agents für Kubernetes im Namespace `azure-arc` bereit:

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

**Ausgabe:**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Helm release deployment succeeded

{
  "aadProfile": {
    "clientAppId": "",
    "serverAppId": "",
    "tenantId": ""
  },
  "agentPublicKeyCertificate": "...",
  "agentVersion": "0.1.0",
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "None"
  },
  "kubernetesVersion": "v1.15.0",
  "location": "eastus",
  "name": "AzureArcTest1",
  "resourceGroup": "AzureArcTest",
  "tags": {},
  "totalNodeCount": 1,
  "type": "Microsoft.Kubernetes/connectedClusters"
}
```

## <a name="verify-connected-cluster"></a>Überprüfen des verbundenen Clusters

Führen Sie den folgenden Befehl aus, um Ihre verbundenen Cluster aufzulisten:

```console
az connectedk8s list -g AzureArcTest -o table
```

**Ausgabe:**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

Sie können diese Ressource auch im [Azure-Portal](https://portal.azure.com/) anzeigen. Öffnen Sie das Portal in Ihrem Browser, und navigieren Sie anhand der zuvor im Befehl `az connectedk8s connect` verwendeten Eingaben für Ressourcenname und Ressourcengruppenname zu der Ressourcengruppe und der Kubernetes-Ressource mit Azure Arc-Unterstützung.

> [!NOTE]
> Nach dem Onboarding des Clusters dauert es ungefähr 5 bis 10 Minuten, bis die Clustermetadaten (Clusterversion, Agent-Version, Anzahl der Knoten usw.) auf der Übersichtsseite der Kubernetes-Ressource, für die Azure Arc aktiviert ist, im Azure-Portal angezeigt wird.

## <a name="connect-using-an-outbound-proxy-server"></a>Herstellen einer Verbindung mit einem ausgehenden Proxyserver

Wenn sich Ihr Cluster hinter einem ausgehenden Proxyserver befindet, müssen Azure CLI und die Kubernetes-Agents mit Arc-Unterstützung Ihre Anforderungen über den ausgehenden Proxyserver weiterleiten:

1. Überprüfen Sie die auf Ihrem Computer installierte Version von `connectedk8s`:

    ```console
    az -v
    ```

    Sie benötigen eine `connectedk8s`-Erweiterungsversion ab  0.2.5, um Agents mit ausgehendem Proxy einrichten zu können. Wenn Sie auf Ihrem Computer eine Version bis 0.2.3 haben, führen Sie die [Aktualisierungsschritte](#before-you-begin) aus, um die neueste Version der Erweiterung auf Ihren Computer zu laden.

2. Legen Sie die für Azure CLI erforderlichen Umgebungsvariablen fest, um den ausgehenden Proxyserver zu verwenden:

    * Wenn Sie die Bash verwenden, führen Sie den folgenden Befehl mit entsprechenden Werten aus:

        ```bash
        export HTTP_PROXY=<proxy-server-ip-address>:<port>
        export HTTPS_PROXY=<proxy-server-ip-address>:<port>
        export NO_PROXY=<cluster-apiserver-ip-address>:<port>
        ```

    * Wenn Sie PowerShell verwenden, führen Sie den folgenden Befehl mit entsprechenden Werten aus:

        ```powershell
        $Env:HTTP_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:HTTPS_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:NO_PROXY = "<cluster-apiserver-ip-address>:<port>"
        ```

3. Führen Sie den connect-Befehl mit den angegebenen Proxyparametern aus:

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> 1. Die Angabe von `excludedCIDR` unter `--proxy-skip-range` ist wichtig, um sicherzustellen, dass die clusterinterne Kommunikation für die Agents nicht unterbrochen wird.
> 2. Während `--proxy-http`, `--proxy-https` und `--proxy-skip-range` für die meisten ausgehenden Proxyumgebungen erwartet werden, ist `--proxy-cert` nur erforderlich, wenn vertrauenswürdige Zertifikate vom Proxy vorhanden sind, die in den Speicher für vertrauenswürdige Zertifikate von Agent-Pods eingefügt werden müssen.
> 3. Die voranstehende Proxyspezifikation wird aktuell nur auf Arc-Agents angewendet, nicht auf die in der sourceControlConfiguration verwendeten Flux-Pods. Das Kubernetes-Team für Arc-Unterstützung arbeitet aktiv an dieser Funktion, die bald verfügbar sein wird.

## <a name="azure-arc-agents-for-kubernetes"></a>Azure Arc-Agents für Kubernetes

Kubernetes mit Azure Arc-Unterstützung stellt einige Operatoren im Namespace `azure-arc` bereit. So können Sie diese Bereitstellungen und Pods mit dem folgenden Befehl anzeigen:

```console
kubectl -n azure-arc get deployments,pods
```

**Ausgabe:**

```console
NAME                                        READY      UP-TO-DATE  AVAILABLE  AGE
deployment.apps/cluster-metadata-operator     1/1             1        1      16h
deployment.apps/clusteridentityoperator       1/1             1        1      16h
deployment.apps/config-agent                  1/1             1        1      16h
deployment.apps/controller-manager            1/1             1        1      16h
deployment.apps/flux-logs-agent               1/1             1        1      16h
deployment.apps/metrics-agent                 1/1             1        1      16h
deployment.apps/resource-sync-agent           1/1             1        1      16h

NAME                                           READY    STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

Kubernetes mit Azure Arc-Unterstützung besteht aus einigen Agents (Operatoren), die in Ihrem Cluster ausgeführt und im Namespace `azure-arc` bereitgestellt werden.

| Agents (Operatoren)                                                                                               | BESCHREIBUNG                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `deployment.apps/config-agent`                                                                                 | Prüft, ob auf den verbundenen Cluster Ressourcen für die Konfiguration der Quellcodeverwaltung angewendet werden und aktualisiert den Konformitätszustand.                                                        |
| `deployment.apps/controller-manager` | Ist ein Operator für Operatoren und koordiniert Interaktionen zwischen Azure Arc-Komponenten.                                      |
| `deployment.apps/metrics-agent`                                                                            | Sammelt Leistungsmetriken von anderen Arc-Agents.                                                                                    |
| `deployment.apps/cluster-metadata-operator`                                                                            | Erfasst Clustermetadaten wie Clusterversion, Knotenanzahl und Version des Azure Arc-Agents.                                                                  |
| `deployment.apps/resource-sync-agent`                                                                            |  Synchronisiert die oben erwähnten Clustermetadaten mit Azure.                                                                  |
| `deployment.apps/clusteridentityoperator`                                                                            |  Kubernetes mit Azure Arc-Unterstützung unterstützt derzeit systemseitig zugewiesene Identitäten. `clusteridentityoperator` verwaltet das von anderen Agents für die Kommunikation mit Azure verwendete MSI-Zertifikat (Managed Service Identity, Verwaltete Dienstidentität).                                                                  |
| `deployment.apps/flux-logs-agent`                                                                            |  Sammelt Protokolle von den bei der Konfiguration der Quellcodeverwaltung bereitgestellten Flux-Operatoren.                                                                  |

## <a name="delete-a-connected-cluster"></a>Löschen eines verbundenen Clusters

Sie können eine `Microsoft.Kubernetes/connectedcluster`-Ressource mithilfe der Azure CLI oder über das Azure-Portal löschen.


* **Löschvorgang mit Azure CLI**: Mit dem folgenden Azure CLI-Befehl kann das Löschen der Kubernetes-Ressource mit Azure Arc-Unterstützung eingeleitet werden.
  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```
  Durch diesen Befehl werden die Ressource `Microsoft.Kubernetes/connectedCluster` und alle zugehörigen `sourcecontrolconfiguration`-Ressourcen in Azure gelöscht. Die Azure CLI verwendet `helm uninstall`, um die im Cluster ausgeführten Agents ebenfalls zu entfernen.

* **Löschvorgang im Azure-Portal**: Beim Löschen der Kubernetes-Ressource mit Azure Arc-Unterstützung im Azure-Portal werden die Ressource `Microsoft.Kubernetes/connectedcluster` und alle zugehörigen `sourcecontrolconfiguration`-Ressourcen in Azure gelöscht. Die im Cluster ausgeführten Agents werden jedoch *nicht* entfernt. 

  Führen Sie den folgenden Befehl aus, um die im Cluster ausgeführten Agents zu entfernen:

  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von GitOps für eine Konfiguration mit Azure Arc-Aktivierung (Vorschau)](./use-gitops-connected-cluster.md)
* [Verwenden von Azure Policy zum Anwenden von Clusterkonfigurationen im großen Stil (Vorschau)](./use-azure-policy.md)
