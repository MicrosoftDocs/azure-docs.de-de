---
title: 'Schnellstart: Erstellen eines AKS-Clusters (Azure Kubernetes Service)'
description: Hier erfahren Sie, wie Sie mithilfe einer Azure Resource Manager-Vorlage schnell einen Kubernetes-Cluster erstellen und eine Anwendung in Azure Kubernetes Service (AKS) bereitstellen.
services: container-service
ms.topic: quickstart
ms.date: 09/11/2020
ms.custom: mvc,subject-armqs, devx-track-azurecli
ms.openlocfilehash: 271913a731a2bdf5af94885b5fe4027c0334853c
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94887500"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-arm-template"></a>Schnellstart: Schnellstart: Bereitstellen eines AKS-Clusters (Azure Kubernetes Service) mithilfe einer ARM-Vorlage

Azure Kubernetes Service (AKS) ist ein verwalteter Kubernetes-Dienst, mit dem Sie schnell Cluster bereitstellen und verwalten können. In diesem Schnellstart stellen Sie einen AKS-Cluster mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage) bereit. In dem Cluster wird eine Anwendung mit mehreren Containern ausgeführt, die ein Web-Front-End und eine Redis-Instanz enthält.

![Abbildung der Navigation zu Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Für diese Schnellstartanleitung werden Grundkenntnisse in Bezug auf die Kubernetes-Konzepte vorausgesetzt. Weitere Informationen finden Sie unter [Grundlegende Kubernetes-Konzepte für Azure Kubernetes Service (AKS)][kubernetes-concepts].

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.61 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

- Um einen AKS-Cluster mithilfe einer Resource Manager-Vorlage zu erstellen, geben Sie einen öffentlichen SSH-Schlüssel und einen Azure Active Directory-Dienstprinzipal an. Alternativ können Sie anstelle eines Dienstprinzipals für Berechtigungen eine [verwaltete Identität](use-managed-identity.md) verwenden. Wenn Sie eine dieser Ressourcen benötigen, finden Sie im folgenden Abschnitt Informationen, andernfalls fahren Sie mit dem Abschnitt [Überprüfen der Vorlage](#review-the-template) fort.

### <a name="create-an-ssh-key-pair"></a>Erstellen eines SSH-Schlüsselpaars

Um auf AKS-Knoten zuzugreifen, stellen Sie eine Verbindung unter Verwendung eines SSH-Schlüsselpaars her. Verwenden Sie den Befehl `ssh-keygen`, um Dateien mit öffentlichen und privaten SSH-Schlüsseln zu generieren. Diese Dateien werden standardmäßig im Verzeichnis *~/.ssh* erstellt. Wenn an dem angegebenen Speicherort bereits ein SSH-Schlüsselpaar mit demselben Namen vorhanden ist, werden diese Dateien überschrieben.

Navigieren Sie zu [https://shell.azure.com](https://shell.azure.com), um Cloud Shell in Ihrem Browser zu öffnen.

Der folgende Befehl erstellt ein SSH-Schlüsselpaar mittels RSA-Verschlüsselung und einer Bitlänge von 2048:

```console
ssh-keygen -t rsa -b 2048
```

Weitere Informationen zum Erstellen von SSH-Schlüsseln finden Sie unter [Erstellen und Verwalten von SSH-Schlüsseln für die Authentifizierung in Azure][ssh-keys].

### <a name="create-a-service-principal"></a>Erstellen eines Dienstprinzipals

Damit ein AKS-Cluster mit anderen Azure-Ressourcen interagieren kann, wird ein Azure Active Directory-Dienstprinzipal verwendet. Erstellen Sie mit dem Befehl [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] einen Dienstprinzipal. Mit dem Parameter `--skip-assignment` wird die Zuweisung zusätzlicher Berechtigungen eingeschränkt. Standardmäßig ist dieser Dienstprinzipal ein Jahr lang gültig. Beachten Sie, dass Sie anstelle eines Dienstprinzipals eine verwaltete Identität verwenden können. Weitere Informationen finden Sie unter [Verwenden verwalteter Identitäten](use-managed-identity.md).

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```json
{
  "appId": "8b1ede42-d407-46c2-a1bc-6b213b04295f",
  "displayName": "azure-cli-2019-04-19-21-42-11",
  "name": "http://azure-cli-2019-04-19-21-42-11",
  "password": "27e5ac58-81b0-46c1-bd87-85b4ef622682",
  "tenant": "73f978cf-87f2-41bf-92ab-2e7ce012db57"
}
```

Notieren Sie sich die App-ID (*appId*) und das Kennwort (*password*). Diese Werte werden in den folgenden Schritten verwendet.

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-aks/).

:::code language="json" source="~/quickstart-templates/101-aks/azuredeploy.json":::

Weitere AKS-Beispiele finden Sie unter [Azure Schnellstartvorlagen][aks-quickstart-templates].

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Klicken Sie auf das folgende Bild, um sich bei Azure anzumelden und eine Vorlage zu öffnen.

    [![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

2. Wählen Sie die folgenden Werte aus, bzw. geben Sie sie ein.

    Im Rahmen dieser Schnellstartanleitung behalten Sie die Standardwerte für *Betriebssystem-Datenträgergröße (GB)* , *Agent-Anzahl*, *Größe der Agent-VM*, *Betriebssystemtyp*, und *Kubernetes-Version* bei. Geben Sie Ihre eigenen Werte für die folgenden Vorlagenparameter an:

    * **Abonnement**: Wählen Sie ein Azure-Abonnement aus.
    * **Ressourcengruppe**: Wählen Sie **Neu erstellen**. Geben Sie einen eindeutigen Namen für die Ressourcengruppe an, z. B. *myResourceGroup*, und wählen Sie dann **OK** aus.
    * **Standort**: Wählen Sie einen Ort aus, z. B. **USA, Osten**.
    * **Clustername**: Geben Sie einen eindeutigen Namen für den AKS-Cluster ein, z. B. *myAKSCluster*.
    * **DNS-Präfix**: Geben Sie ein eindeutiges DNS-Präfix für Ihren Cluster ein, z. B. *myakscluster*.
    * **Benutzername des Linux-Administrators**: Geben Sie einen Benutzernamen ein, um mithilfe von SSH eine Verbindung herzustellen, z. B. *azureuser*.
    * **Öffentlicher SSH RSA-Schlüssel**: Kopieren Sie den *öffentlichen* Teil Ihres SSH-Schlüsselpaar, und fügen Sie ihn ein (standardmäßig der Inhalt von *~/.ssh/id_rsa.pub*).
    * **Client-ID des Dienstprinzipals**: Kopieren Sie die *appId* Ihres Dienstprinzipals aus dem Befehl `az ad sp create-for-rbac`, und fügen Sie sie ein.
    * **Clientgeheimnis des Dienstprinzipals**: Kopieren Sie das *password* Ihres Dienstprinzipals aus dem Befehl `az ad sp create-for-rbac`, und fügen Sie es ein.
    * **Ich stimme den oben genannten Geschäftsbedingungen zu**: Aktivieren Sie dieses Kontrollkästchen, um zuzustimmen.

    ![Resource Manager-Vorlage zum Erstellen eines Azure Kubernetes Service-Clusters im Portal](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. Wählen Sie die Option **Kaufen**.

Die Erstellung des AKS-Clusters dauert einige Minuten. Warten Sie, bis der Cluster erfolgreich bereitgestellt wurde, bevor Sie mit dem nächsten Schritt fortfahren.

## <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung

### <a name="connect-to-the-cluster"></a>Herstellen einer Verbindung mit dem Cluster

Verwenden Sie zum Verwalten eines Kubernetes-Clusters den Kubernetes-Befehlszeilenclient [kubectl][kubectl]. Bei Verwendung von Azure Cloud Shell ist `kubectl` bereits installiert. Verwenden Sie für die lokale Installation von `kubectl` den Befehl [az aks install-cli][az-aks-install-cli]:

```azurecli
az aks install-cli
```

Mit dem Befehl [az aks get-credentials][az-aks-get-credentials] können Sie `kubectl` für die Verbindungsherstellung mit Ihrem Kubernetes-Cluster konfigurieren. Mit diesem Befehl werden die Anmeldeinformationen heruntergeladen, und die Kubernetes-Befehlszeilenschnittstelle wird für deren Verwendung konfiguriert.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Überprüfen Sie die Verbindung mit Ihrem Cluster mithilfe des Befehls [kubectl get][kubectl-get], um eine Liste der Clusterknoten zurückzugeben.

```console
kubectl get nodes
```

Die folgende Beispielausgabe zeigt die in den vorherigen Schritten erstellten Knoten. Vergewissern Sie sich, dass der Status aller Knoten *Bereit* ist:

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6
aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
```

### <a name="run-the-application"></a>Ausführen der Anwendung

Eine Kubernetes-Manifestdatei definiert einen gewünschten Zustand (Desired State) für den Cluster – also beispielsweise, welche Containerimages ausgeführt werden sollen. In dieser Schnellstartanleitung wird ein Manifest verwendet, um alle Objekte zu erstellen, die zum Ausführen der Azure Vote-Anwendung benötigt werden. Dieses Manifest umfasst zwei [Kubernetes-Bereitstellungen][kubernetes-deployment]: eine für die Azure Vote-Python-Beispielanwendungen und eine für eine Redis-Instanz. Außerdem werden zwei [Kubernetes-Dienste][kubernetes-service] erstellt: ein interner Dienst für die Redis-Instanz und ein externer Dienst, über den aus dem Internet auf die Azure Vote-Anwendung zugegriffen wird.

Erstellen Sie eine Datei namens `azure-vote.yaml`, und fügen Sie die folgende YAML-Definition ein. Bei Verwendung von Azure Cloud Shell kann diese Datei mit `vi` oder `nano` erstellt werden – genau wie bei der Verwendung eines virtuellen oder physischen Systems:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
        env:
        - name: ALLOW_EMPTY_PASSWORD
          value: "yes"
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Stellen Sie die Anwendung über den Befehl [kubectl apply][kubectl-apply] bereit, und geben Sie den Namen Ihres YAML-Manifests an:

```console
kubectl apply -f azure-vote.yaml
```

In der folgenden Beispielausgabe sind die erfolgreich erstellten Bereitstellungen und Dienste aufgeführt:

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

### <a name="test-the-application"></a>Testen der Anwendung

Wenn die Anwendung ausgeführt wird, macht ein Kubernetes-Dienst das Anwendungs-Front-End im Internet verfügbar. Dieser Vorgang kann einige Minuten dauern.

Verwenden Sie zum Überwachen des Fortschritts den Befehl [kubectl get service][kubectl-get] mit dem Argument `--watch`.

```console
kubectl get service azure-vote-front --watch
```

Die externe IP-Adresse (*EXTERNAL-IP*) für den Dienst *azure-vote-front* wird zunächst als *ausstehend* angezeigt.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Sobald die externe IP-Adresse (*EXTERNAL-IP*) von pending (*ausstehend*) in eine tatsächliche öffentliche IP-Adresse geändert wurde, verwenden Sie `CTRL-C`, um die `kubectl`-Überwachung zu beenden. Die folgende Beispielausgabe zeigt eine gültige öffentliche IP-Adresse, die dem Dienst zugewiesen ist:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Öffnen Sie die externe IP-Adresse Ihres Diensts in einem Webbrowser, um die Azure-Abstimmungs-App in Aktion zu sehen.

![Abbildung der Navigation zu Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn der Cluster nicht mehr benötigt wird, entfernen Sie mit dem Befehl [az group delete][az-group-delete] die Ressourcengruppe, den Containerdienst und alle zugehörigen Ressourcen.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Wenn Sie den Cluster löschen, wird der vom AKS-Cluster verwendete Azure Active Directory-Dienstprinzipal nicht entfernt. Schritte zum Entfernen des Dienstprinzipals finden Sie unter den [Überlegungen zum AKS-Dienstprinzipal und dessen Löschung][sp-delete]. Wenn Sie eine verwaltete Identität verwendet haben, wird die Identität von der Plattform verwaltet und muss nicht entfernt werden.

## <a name="get-the-code"></a>Abrufen des Codes

In dieser Schnellstartanleitung wurden vorab erstellte Containerimages verwendet, um eine Kubernetes-Bereitstellung zu erstellen. Der dazugehörige Anwendungscode, die Dockerfile-Datei und die Kubernetes-Manifestdatei sind auf GitHub verfügbar.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen Kubernetes-Cluster und eine Anwendung mit mehreren Containern dafür bereitgestellt. [Greifen Sie auf das Kubernetes-Webdashboard zu][kubernetes-dashboard] (für den Cluster, den Sie erstellt haben).

Weitere Informationen zu Azure Container Service sowie ein vollständiges Beispiel vom Code bis zur Bereitstellung finden Sie im Kubernetes-Clustertutorial.

> [!div class="nextstepaction"]
> [AKS-Tutorial][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: ../dev-spaces/index.yml
[aks-quickstart-templates]: https://azure.microsoft.com/resources/templates/?term=Azure+Kubernetes+Service

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/insights/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[ssh-keys]: ../virtual-machines/linux/create-ssh-keys-detailed.md
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
