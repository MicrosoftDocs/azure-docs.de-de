---
title: Bereitstellen einer Java-Anwendung mit Open Liberty oder WebSphere Liberty in einem Azure Kubernetes Service (AKS)-Cluster
description: Stellen Sie eine Java-Anwendung mit Open Liberty oder WebSphere Liberty in einem Azure Kubernetes Service (AKS)-Cluster bereit.
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 02/01/2021
keywords: java, jakartaee, javaee, microprofile, open-liberty, websphere-liberty, aks, kubernetes
ms.openlocfilehash: d0e6f2fea6894378da736ba83a90ee28402ec7f9
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007130"
---
# <a name="deploy-a-java-application-with-open-liberty-or-websphere-liberty-on-an-azure-kubernetes-service-aks-cluster"></a>Bereitstellen einer Java-Anwendung mit Open Liberty oder WebSphere Liberty in einem Azure Kubernetes Service (AKS)-Cluster

Dieser Artikel demonstriert Folgendes:  
* Führen Sie Ihre Java-, Java EE-, Jakarta EE- oder MicroProfile-Anwendung in der Open Liberty- oder WebSphere Liberty-Runtime aus.
* Erstellen Sie das Docker-Image der Anwendung mit Open Liberty-Containerimages.
* Stellen Sie die containerisierte Anwendung mithilfe des Open Liberty-Operators in einem AKS-Cluster bereit.   

Der Open Liberty-Operator vereinfacht die Bereitstellung und Verwaltung von Anwendungen, die in Kubernetes-Clustern ausgeführt werden. Sie können mit dem Open Liberty-Operator auch komplexere Vorgänge ausführen, z. B. die Erfassung von Ablaufverfolgungen und Speicherabbildern. 

Weitere Informationen zu Open Liberty finden Sie auf der [Open Liberty-Projektseite](https://openliberty.io/). Weitere Informationen zu IBM WebSphere Liberty finden Sie auf der [WebSphere Liberty-Produktseite](https://www.ibm.com/cloud/websphere-liberty).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Für diesen Artikel ist die aktuelle Azure CLI-Version erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.
* Bei lokaler Ausführung der Befehle in diesem Leitfaden (statt in Azure Cloud Shell):
  * Bereiten Sie einen lokalen Computer mit installiertem UNIX-ähnlichem Betriebssystem vor (z. B. Ubuntu, macOS, Windows-Subsystem für Linux).
  * Installieren Sie eine Java SE-Implementierung (z. B. [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)).
  * Installieren Sie [Maven](https://maven.apache.org/download.cgi) 3.5.0 oder höher.
  * Installieren Sie [Docker](https://docs.docker.com/get-docker/) für Ihr Betriebssystem.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist eine logische Gruppe, in der Azure-Ressourcen bereitgestellt und verwaltet werden.  

Erstellen Sie am Standort *eastus* mithilfe des Befehls [az group create](/cli/azure/group#az_group_create) die Ressourcengruppe *java-liberty-project*. Diese Ressourcengruppe wird zu einem späteren Zeitpunkt zum Erstellen der Azure Container Registry-Instanz (ACR) und des AKS-Clusters verwendet. 

```azurecli-interactive
RESOURCE_GROUP_NAME=java-liberty-project
az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-an-acr-instance"></a>Erstellen einer ACR-Instanz

Verwenden Sie den Befehl [az acr create](/cli/azure/acr#az_acr_create) zum Erstellen der ACR-Instanz. Im folgenden Beispiel wird die ACR-Instanz *youruniqueacrname* erstellt. Stellen Sie sicher, dass *youruniqueacrname* in Azure eindeutig ist.

```azurecli-interactive
REGISTRY_NAME=youruniqueacrname
az acr create --resource-group $RESOURCE_GROUP_NAME --name $REGISTRY_NAME --sku Basic --admin-enabled
```

Nach kurzer Zeit sollte eine JSON-Ausgabe mit Folgendem angezeigt werden:

```output
  "provisioningState": "Succeeded",
  "publicNetworkAccess": "Enabled",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-acr-instance"></a>Herstellen einer Verbindung mit der ACR-Instanz

Sie müssen sich bei der ACR-Instanz anmelden, bevor Sie ein Image an diese pushen können. Führen Sie die folgenden Befehle zum Überprüfen der Verbindung aus:

```azurecli-interactive
LOGIN_SERVER=$(az acr show -n $REGISTRY_NAME --query 'loginServer' -o tsv)
USER_NAME=$(az acr credential show -n $REGISTRY_NAME --query 'username' -o tsv)
PASSWORD=$(az acr credential show -n $REGISTRY_NAME --query 'passwords[0].value' -o tsv)

docker login $LOGIN_SERVER -u $USER_NAME -p $PASSWORD
```

Wenn Sie sich bei der ACR-Instanz erfolgreich angemeldet haben, sollte am Ende der Befehlsausgabe `Login Succeeded` angezeigt werden.

## <a name="create-an-aks-cluster"></a>Erstellen eines AKS-Clusters

Erstellen Sie mithilfe des Befehls [az aks create](/cli/azure/aks#az_aks_create) einen AKS-Cluster. Im folgenden Beispiel wird ein Cluster mit dem Namen *myAKSCluster* mit einem Knoten erstellt. Dieser Schritt dauert einige Minuten.

```azurecli-interactive
CLUSTER_NAME=myAKSCluster
az aks create --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME --node-count 1 --generate-ssh-keys --enable-managed-identity
```

Nach wenigen Minuten ist die Ausführung des Befehls abgeschlossen, und es werden Informationen zum Cluster im JSON-Format zurückgegeben, einschließlich der folgenden:

```output
  "nodeResourceGroup": "MC_java-liberty-project_myAKSCluster_eastus",
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-aks-cluster"></a>Herstellen einer Verbindung mit dem AKS-Cluster

Verwenden Sie zum Verwalten eines Kubernetes-Clusters den Kubernetes-Befehlszeilenclient [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/). Bei Verwendung von Azure Cloud Shell ist `kubectl` bereits installiert. Verwenden Sie für die lokale Installation von `kubectl` den Befehl [az aks install-cli](/cli/azure/aks#az_aks_install_cli):

```azurecli-interactive
az aks install-cli
```

Mit dem Befehl [az aks get-credentials](/cli/azure/aks#az_aks_get_credentials) können Sie `kubectl` für die Verbindungsherstellung mit Ihrem Kubernetes-Cluster konfigurieren. Mit diesem Befehl werden die Anmeldeinformationen heruntergeladen, und die Kubernetes-Befehlszeilenschnittstelle wird für deren Verwendung konfiguriert.

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME --overwrite-existing
```

> [!NOTE]
> Der obige Befehl verwendet den Standardspeicherort für die [Kubernetes-Konfigurationsdatei](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/), d. h. `~/.kube/config`. Sie können mit *--file* einen anderen Speicherort für Ihre Kubernetes-Konfigurationsdatei angeben.

Überprüfen Sie die Verbindung mit Ihrem Cluster mithilfe des Befehls [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get), um eine Liste der Clusterknoten zurückzugeben.

```azurecli-interactive
kubectl get nodes
```

Die folgende Beispielausgabe zeigt den in den vorherigen Schritten erstellten Knoten. Vergewissern Sie sich, dass der Knoten den Status *Bereit* hat:

```output
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-xxxxxxxx-yyyyyyyyyy   Ready    agent   76s     v1.18.10
```

## <a name="install-open-liberty-operator"></a>Installieren von Open Liberty Operator

Nachdem Sie den Cluster erstellt und eine Verbindung damit hergestellt haben, installieren Sie den [Open Liberty Operator](https://github.com/OpenLiberty/open-liberty-operator/tree/master/deploy/releases/0.7.0) durch Ausführung der folgenden Befehle.

```azurecli-interactive
OPERATOR_NAMESPACE=default
WATCH_NAMESPACE='""'

# Install Custom Resource Definitions (CRDs) for OpenLibertyApplication
kubectl apply -f https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-crd.yaml

# Install cluster-level role-based access
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-cluster-rbac.yaml \
      | sed -e "s/OPEN_LIBERTY_OPERATOR_NAMESPACE/${OPERATOR_NAMESPACE}/" \
      | kubectl apply -f -

# Install the operator
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-operator.yaml \
      | sed -e "s/OPEN_LIBERTY_WATCH_NAMESPACE/${WATCH_NAMESPACE}/" \
      | kubectl apply -n ${OPERATOR_NAMESPACE} -f -
```

## <a name="build-application-image"></a>Erstellen des Anwendungsimages

Zum Bereitstellen und Ausführen Ihrer Liberty-Anwendung im AKS-Cluster müssen Sie Ihre Anwendung mit [Open Liberty-Containerimages](https://github.com/OpenLiberty/ci.docker) oder [WebSphere Liberty-Containerimages](https://github.com/WASdev/ci.docker) als Docker-Image containerisieren.

1. Klonen Sie den Beispielcode für diesen Leitfaden. Das Beispiel befindet sich auf [GitHub](https://github.com/Azure-Samples/open-liberty-on-aks).
1. Wechseln Sie in das Verzeichnis `javaee-app-simple-cluster` des lokalen Klons.
1. Führen Sie `mvn clean package` zum Verpacken der Anwendung aus.
1. Führen Sie zum Testen der Anwendung `mvn liberty:dev` aus. Bei erfolgreicher Ausführung sollte in der Befehlsausgabe `The defaultServer server is ready to run a smarter planet.` angezeigt werden. Verwenden Sie `CTRL-C`, um die Anwendung zu beenden.
1. Führen Sie einen der folgenden Befehle aus, um das Anwendungsimage zu erstellen und es an die ACR-Instanz zu pushen.
   * Erstellen Sie mit Open Liberty Base ein Image, wenn Sie lieber Open Liberty als Lightweight Open Source Java™-Runtime verwenden möchten:

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary Open Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME .
     ```

   * Erstellen Sie mit WebSphere Liberty ein Basisimage, wenn Sie lieber eine kommerzielle Version von Open Liberty verwenden möchten:

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary WebSphere Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME --file=Dockerfile-wlp .
     ```

## <a name="deploy-application-on-the-aks-cluster"></a>Bereitstellen der Anwendung auf dem AKS-Cluster

Führen Sie die folgenden Schritte aus, um die Liberty-Anwendung im AKS-Cluster bereitzustellen.

1. Erstellen Sie ein Pullgeheimnis, damit der AKS-Cluster zum Pullen des Images aus der ACR-Instanz authentifiziert wird.

   ```azurecli-interactive
   kubectl create secret docker-registry acr-secret \
      --docker-server=${LOGIN_SERVER} \
      --docker-username=${USER_NAME} \
      --docker-password=${PASSWORD}
   ```

1. Überprüfen Sie, ob das aktuelle Arbeitsverzeichnis `javaee-app-simple-cluster` Ihres lokalen Klons ist.
1. Führen Sie die folgenden Befehle aus, um Ihre Liberty-Anwendung mit drei Replikaten im AKS-Cluster bereitzustellen. Die Befehlsausgabe wird auch inline angezeigt.

   ```azurecli-interactive
   # Create OpenLibertyApplication "javaee-app-simple-cluster"
   cat openlibertyapplication.yaml | sed -e "s/\${Container_Registry_URL}/${LOGIN_SERVER}/g" | sed -e "s/\${REPLICAS}/3/g" | kubectl apply -f -

   openlibertyapplication.openliberty.io/javaee-app-simple-cluster created

   # Check if OpenLibertyApplication instance is created
   kubectl get openlibertyapplication javaee-app-simple-cluster

   NAME                        IMAGE                                                   EXPOSED   RECONCILED   AGE
   javaee-app-simple-cluster   youruniqueacrname.azurecr.io/javaee-cafe-simple:1.0.0             True         59s

   # Check if deployment created by Operator is ready
   kubectl get deployment javaee-app-simple-cluster --watch

   NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-app-simple-cluster   0/3     3            0           20s
   ```

1. Warten Sie, bis unter der Spalte `READY` der Wert `3/3` und unter der Spalte `AVAILABLE` der Wert `3` angezeigt wird. Verwenden Sie dann `CTRL-C`, um den Überwachungsprozess `kubectl` zu beenden.

### <a name="test-the-application"></a>Testen der Anwendung

Wenn die Anwendung ausgeführt wird, macht ein Kubernetes-Lastenausgleichsdienst das Anwendungs-Front-End im Internet verfügbar. Es kann eine Weile dauern, bis dieser Vorgang abgeschlossen ist.

Verwenden Sie zum Überwachen des Fortschritts den Befehl [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) mit dem Argument `--watch`.

```azurecli-interactive
kubectl get service javaee-app-simple-cluster --watch

NAME                        TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)          AGE
javaee-app-simple-cluster   LoadBalancer   10.0.251.169   52.152.189.57   80:31732/TCP     68s
```

Nachdem die externe IP-Adresse (*EXTERNAL-IP*) von *ausstehend* in eine tatsächliche öffentliche IP-Adresse geändert wurde, verwenden Sie `CTRL-C`, um die `kubectl`-Überwachung zu beenden.

Öffnen Sie einen Webbrowser für die externe IP-Adresse Ihres Diensts (`52.152.189.57` für das vorstehende Beispiel), um die Startseite der Anwendung anzuzeigen. Der Podname Ihrer Anwendungsreplikate sollte oben links auf der Seite angezeigt werden. Warten Sie ein paar Minuten, und aktualisieren Sie die Seite. Es wird aufgrund des vom AKS-Cluster bereitgestellten Lastenausgleichs ein anderer Podname angezeigt.

:::image type="content" source="./media/howto-deploy-java-liberty-app/deploy-succeeded.png" alt-text="Java Liberty-Anwendung wurde in AKS erfolgreich bereitgestellt":::

>[!NOTE]
> - Zurzeit verwendet die Anwendung nicht HTTPS. Es empfiehlt sich, [TLS mit Ihren eigenen Zertifikaten zu aktivieren](ingress-own-tls.md).

## <a name="clean-up-the-resources"></a>Bereinigen der Ressourcen

Zum Vermeiden von Azure-Gebühren sollten Sie nicht benötigte Ressourcen bereinigen.  Wenn der Cluster nicht mehr benötigt wird, entfernen Sie mit dem Befehl [az group delete](/cli/azure/group#az_group_delete) die Ressourcengruppe, den Containerdienst, die Containerregistrierung und alle zugehörigen Ressourcen.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME --yes --no-wait
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie über die in diesem Handbuch verwendeten Verweise:

* [Azure Kubernetes Service](https://azure.microsoft.com/free/services/kubernetes-service/)
* [Open Liberty](https://openliberty.io/)
* [Open Liberty Operator](https://github.com/OpenLiberty/open-liberty-operator)
* [Open Liberty-Serverkonfiguration](https://openliberty.io/docs/ref/config/)
* [Liberty Maven-Plug-In](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Open Liberty-Containerimages](https://github.com/OpenLiberty/ci.docker)
* [WebSphere Liberty-Containerimages](https://github.com/WASdev/ci.docker)
