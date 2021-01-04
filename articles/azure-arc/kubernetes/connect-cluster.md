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
ms.openlocfilehash: 0166ac0b19208a60f360792fb67ccc050a014ce1
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94833247"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Herstellen einer Verbindung für einen Azure Arc-fähigen Kubernetes-Cluster (Vorschau)

In diesem Dokument wird der Vorgang zum Herstellen einer Verbindung mit einem von Cloud Native Computing Foundation (CNFC) zertifizierten Kubernetes-Cluster erläutert, z. B. eine AKS-Engine in Azure oder Azure Stack Hub oder GKE-, EKS- und VMware vSphere-Cluster in Azure Arc.

## <a name="before-you-begin"></a>Voraussetzungen

Vergewissern Sie sich, dass die folgenden Anforderungen erfüllt sind:

* Ein ordnungsgemäß funktionierender Kubernetes-Cluster. Wenn Sie über keinen Kubernetes-Cluster verfügen, können Sie eine der folgenden Anleitungen verwenden, um einen Testcluster zu erstellen:
  * Erstellen eines Kubernetes-Clusters mithilfe von [Kubernetes in Docker (kind)](https://kind.sigs.k8s.io/)
  * Erstellen eines Kubernetes-Clusters mithilfe von Docker für [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) oder [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)
* Sie benötigen eine kubeconfig-Datei, um auf den Cluster und die Rolle des Clusteradministrators auf dem Cluster zuzugreifen, um Arc-fähige Kubernetes-Agents bereitstellen zu können.
* Der für die Befehle `az login` und `az connectedk8s connect` verwendete Benutzer oder Dienstprinzipal muss über die Berechtigungen „Lesen“ und „Schreiben“ für den Ressourcentyp „Microsoft.Kubernetes/connectedclusters“ verfügen. Die Rolle „Kubernetes-Cluster – Azure Arc-Onboarding“ verfügt über diese Berechtigungen und kann für Rollenzuweisungen für den Benutzer oder Dientsprinzipal verwendet werden.
* Helm 3 ist für das Onboarding des Clusters mithilfe der connectedk8s-Erweiterung erforderlich. [Installieren Sie die neueste Version von Helm 3](https://helm.sh/docs/intro/install), um diese Anforderung zu erfüllen.
* Azure CLI, Version 2.3 und höher, ist zum Installieren der Azure Arc-fähigen Kubernetes CLI-Erweiterungen erforderlich. [Installieren Sie Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true), oder aktualisieren Sie auf die neueste Version, um sicherzustellen, dass Sie über Azure CLI, Version 2.3 oder höher, verfügen.
* Installieren der Arc-fähigen Kubernetes-CLI-Erweiterungen:
  
  Installieren Sie die Erweiterung `connectedk8s`, mit der Sie Kubernetes-Cluster mit Azure verbinden können:
  
  ```console
  az extension add --name connectedk8s
  ```
  
  Installieren Sie die Erweiterung `k8sconfiguration`:
  
  ```console
  az extension add --name k8sconfiguration
  ```
  
  Wenn Sie diese Erweiterungen später aktualisieren möchten, führen Sie die folgenden Befehle aus:
  
  ```console
  az extension update --name connectedk8s
  az extension update --name k8sconfiguration
  ```

## <a name="supported-regions"></a>Unterstützte Regionen

* East US
* Europa, Westen

## <a name="network-requirements"></a>Netzwerkanforderungen

Azure Arc-Agents müssen über die folgenden Protokolle/Ports/ausgehenden URLs verfügen, um zu funktionieren.

* TCP auf Port 443 --> `https://:443`
* TCP auf Port 9418 --> `git://:9418`

| Endpunkt (DNS)                                                                                               | BESCHREIBUNG                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Erforderlich, damit der Agent eine Verbindung mit Azure herstellen und den Cluster registrieren kann                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Datenebenen-Endpunkt, über den der Agent Statusinformationen mithilfe von Push übermitteln und Konfigurationsinformationen abrufen kann                                      |
| `https://login.microsoftonline.com`                                                                            | Erforderlich zum Abrufen und Aktualisieren von Azure Resource Manager-Token                                                                                    |
| `https://mcr.microsoft.com`                                                                            | Erforderlich zum Pullen von Containerimages für Azure Arc-Agents                                                                  |
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`                                                                            |  Erforderlich zum Pullen vom System zugewiesener Zertifikate für verwaltete Identitäten                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Registrieren der beiden Anbieter für Kubernetes mit Azure Arc-Aktivierung:

```console
az provider register --namespace Microsoft.Kubernetes

az provider register --namespace Microsoft.KubernetesConfiguration
```

Die Registrierung ist ein asynchroner Prozess. Sie kann ca. 10 Minuten dauern. Sie können den Registrierungsprozess mit den folgenden Befehlen überwachen:

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

Als Nächstes verbinden Sie Ihren Kubernetes-Cluster mit Azure. Führen Sie für `az connectedk8s connect` die folgenden Schritte aus:

1. Überprüfen Sie über `KUBECONFIG`, `~/.kube/config` oder `--kube-config` die Konnektivität mit Ihrem Kubernetes-Cluster.
1. Stellen Sie Azure Arc-Agents für Kubernetes im Namespace `azure-arc` mit Helm 3 bereit.

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

Listen Sie Ihre verbundenen Cluster auf:

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

Sie können diese Ressource auch im [Azure-Portal](https://portal.azure.com/) anzeigen. Nachdem Sie das Portal in Ihrem Browser geöffnet haben, navigieren Sie anhand der früher im Befehl `az connectedk8s connect` verwendeten Eingaben für Ressourcenname und Ressourcengruppenname zu der Ressourcengruppe und der Azure Arc-fähigen Kubernetes-Ressource.

> [!NOTE]
> Nach dem Onboarding des Clusters dauert es ungefähr 5 bis 10 Minuten, bis die Clustermetadaten (Clusterversion, Agent-Version, Anzahl der Knoten) auf der Übersichtsseite der Kubernetes-Ressource, für die Azure Arc aktiviert ist, im Azure-Portal angezeigt wird.

## <a name="connect-using-an-outbound-proxy-server"></a>Herstellen einer Verbindung mit einem ausgehenden Proxyserver

Wenn sich Ihr Cluster hinter einem ausgehenden Proxyserver befindet, müssen Azure CLI und die für Arc aktivierten Kubernetes-Agents Ihre Anforderungen über den ausgehenden Proxyserver weiterleiten. Diese Konfiguration ermöglicht das folgende Verhalten:

1. Überprüfen Sie die auf Ihrem Computer installierte Version von `connectedk8s` mit diesem Befehl:

    ```console
    az -v
    ```

    Sie benötigen eine `connectedk8s`-Erweiterungsversion >= 0.2.5, um Agents mit ausgehendem Proxy einrichten zu können. Wenn Sie auf Ihrem Computer eine Version < 0.2.3 haben, führen Sie die [Aktualisierungsschritte](#before-you-begin) aus, um die neueste Version der Erweiterung auf Ihren Computer zu laden.

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
> 1. Die Angabe von „excludedCIDR“ unter „--proxy-skip-range“ ist wichtig, um sicherzustellen, dass die clusterinterne Kommunikation für die Agents nicht unterbrochen wird.
> 2. Während „--proxy-http“, „--proxy-https“ und „--proxy-skip-range“ für die meisten ausgehenden Proxyumgebungen erwartet werden, ist „--proxy-cert“ nur erforderlich, wenn vertrauenswürdige Zertifikate vom Proxy vorhanden sind, die in den Speicher für vertrauenswürdige Zertifikate von Agent-Pods eingefügt werden müssen.
> 3. Die voranstehende Proxyspezifikation wird aktuell nur auf Arc-Agents angewendet, nicht auf die in der sourceControlConfiguration verwendeten Flux-Pods. Das für Arc aktivierte Kubernetes-Team arbeitet aktiv an dieser Funktion, die bald verfügbar sein wird.

## <a name="azure-arc-agents-for-kubernetes"></a>Azure Arc-Agents für Kubernetes

Kubernetes mit Azure Arc-Aktivierung stellt einige Operatoren im Namespace `azure-arc` bereit. So können Sie diese Bereitstellungen und Pods anzeigen:

```console
kubectl -n azure-arc get deployments,pods
```

**Ausgabe:**

```console
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

Kubernetes mit Azure Arc-Aktivierung besteht aus einigen Agents (Operatoren), die in Ihrem Cluster ausgeführt und im Namespace `azure-arc` bereitgestellt werden.

* `deployment.apps/config-agent` prüft, ob auf den verbundenen Cluster Ressourcen für die Konfiguration der Quellcodeverwaltung angewendet werden und aktualisiert den Konformitätszustand.
* `deployment.apps/controller-manager` ist ein Operator für Operatoren und koordiniert Interaktionen zwischen Azure Arc-Komponenten.
* `deployment.apps/metrics-agent` erfasst Metriken für andere Arc-Agents, um sicherzustellen, dass diese Agents eine optimale Leistung aufweisen.
* `deployment.apps/cluster-metadata-operator` erfasst Clustermetadaten: Clusterversion, Knotenanzahl und Version des Azure Arc-Agents.
* `deployment.apps/resource-sync-agent` synchronisiert die oben erwähnten Clustermetadaten mit Azure.
* `deployment.apps/clusteridentityoperator`: Kubernetes mit Azure Arc-Aktivierung unterstützt derzeit systemseitig zugewiesene Identitäten. clusteridentityoperator verwaltet das von anderen Agents für die Kommunikation mit Azure verwendete MSI-Zertifikat (Managed Service Identity, Verwaltete Dienstidentität).
* `deployment.apps/flux-logs-agent` sammelt Protokolle von den im Rahmen der Konfiguration der Quellcodeverwaltung bereitgestellten Flux-Operatoren.

## <a name="delete-a-connected-cluster"></a>Löschen eines verbundenen Clusters

Sie können eine `Microsoft.Kubernetes/connectedcluster`-Ressource mithilfe der Azure CLI oder über das Azure-Portal löschen.


* **Löschvorgang mit Azure CLI**: Der folgende Azure CLI-Befehl kann verwendet werden, um die Löschung der Azure Arc-fähigen Kubernetes-Ressource einzuleiten.
  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```
  Durch diesen Befehl werden die Ressource `Microsoft.Kubernetes/connectedCluster` und alle zugehörigen `sourcecontrolconfiguration`-Ressourcen in Azure gelöscht. Azure CLI verwendet die Deinstallation von Helm, um die auf dem Cluster ausgeführten Agents ebenfalls zu entfernen.

* **Löschvorgang im Azure-Portal**: Beim Löschen der Azure Arc-fähigen Kubernetes-Ressource im Azure-Portal werden die `Microsoft.Kubernetes/connectedcluster`-Ressource und alle zugehörigen `sourcecontrolconfiguration`-Ressourcen in Azure gelöscht, jedoch nicht die auf dem Cluster ausgeführten Agents. Führen Sie den folgenden Befehl aus, um die auf dem Cluster ausgeführten Agents zu löschen.

  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von GitOps für eine Konfiguration mit Azure Arc-Aktivierung (Vorschau)](./use-gitops-connected-cluster.md)
* [Verwenden von Azure Policy zum Anwenden von Clusterkonfigurationen im großen Stil (Vorschau)](./use-azure-policy.md)
