---
title: 'Tutorial: Bereitstellen von WordPress in einem AKS-Cluster mit MySQL Flexible Server unter Verwendung der Azure CLI'
description: Hier erfahren Sie, wie Sie WordPress in AKS mit Azure Database for MySQL – Flexible Server schnell erstellen und bereitstellen.
ms.service: mysql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 11/25/2020
ms.custom: mvc
ms.openlocfilehash: 31ad9450c775e5e4e7ae543241b48f8c372ad9ee
ms.sourcegitcommit: 003ac3b45abcdb05dc4406661aca067ece84389f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96749208"
---
# <a name="tutorial-deploy-wordpress-app-on-aks-with-azure-database-for-mysql---flexible-server"></a>Tutorial: Bereitstellen einer WordPress-App in AKS mit Azure Database for MySQL – Flexible Server

In dieser Schnellstartanleitung wird eine WordPress-Anwendung in einem AKS-Cluster (Azure Kubernetes Service) mit Azure Database for MySQL – Flexible Server (Vorschau) unter Verwendung der Azure CLI bereitgestellt. [AKS](../../aks/intro-kubernetes.md) ist ein verwalteter Kubernetes-Dienst, mit dem Sie Cluster schnell bereitstellen und verwalten können. [Azure Database for MySQL – Flexible Server (Vorschau)](overview.md) ist ein vollständig verwalteter Datenbankdienst, der eine differenziertere Steuerung und mehr Flexibilität bei den Verwaltungsfunktionen und Konfigurationseinstellungen der Datenbank bietet. Flexible Server befindet sich aktuell in der Vorschauphase.

> [!NOTE]
> - Azure Database for MySQL Flexible Server befindet sich aktuell in der öffentlichen Vorschau.
> - Für diese Schnellstartanleitung werden Grundkenntnisse bei Kubernetes-Konzepten, WordPress und MySQL vorausgesetzt.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist die aktuelle Azure CLI-Version erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

> [!NOTE]
> Wenn Sie die Befehle in diesem Schnellstart (anstelle von Azure Cloud Shell) ausführen, stellen Sie sicher, dass Sie die Befehle als Administrator ausführen.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist eine logische Gruppe, in der Azure-Ressourcen bereitgestellt und verwaltet werden. Erstellen Sie mithilfe des Befehls [az group create][az-group-create] eine Ressourcengruppe namens *wordpress-project* am Standort *eastus*.

```azurecli-interactive
az group create --name wordpress-project --location eastus
```

> [!NOTE]
> Der Standort für die Ressourcengruppe ist der Ort, an dem die Metadaten der Ressourcengruppen gespeichert werden. Darüber hinaus werden dort Ihre Ressourcen in Azure ausgeführt, sofern Sie im Rahmen der Ressourcenerstellung keine andere Region angeben.

Die folgende Beispielausgabe zeigt, dass die Ressourcengruppe erfolgreich erstellt wurde:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/wordpress-project",
  "location": "eastus",
  "managedBy": null,
  "name": "wordpress-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>Erstellen eines ACS-Clusters

Erstellen Sie mithilfe des Befehls [az aks create](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-create) einen AKS-Cluster. Im folgenden Beispiel wird ein Cluster mit dem Namen *myAKSCluster* mit einem Knoten erstellt. Dieser Schritt dauert einige Minuten.

```azurecli-interactive
az aks create --resource-group wordpress-project --name wordpresscluster--node-count 1 --generate-ssh-keys
```

Nach wenigen Minuten ist die Ausführung des Befehls abgeschlossen, und es werden Informationen zum Cluster im JSON-Format zurückgegeben.

> [!NOTE]
> Beim Erstellen eines AKS-Clusters wird automatisch eine zweite Ressourcengruppe erstellt, um die AKS-Ressourcen zu speichern. Weitere Informationen finden Sie unter [Warum werden zwei Ressourcengruppen mit AKS erstellt?](../../aks/faq.md#why-are-two-resource-groups-created-with-aks).

## <a name="connect-to-the-cluster"></a>Herstellen einer Verbindung mit dem Cluster

Verwenden Sie zum Verwalten eines Kubernetes-Clusters den Kubernetes-Befehlszeilenclient [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/). Bei Verwendung von Azure Cloud Shell ist `kubectl` bereits installiert. Verwenden Sie für die lokale Installation von `kubectl` den Befehl [az aks install-cli](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-install-cli):

```azurecli-interactive
az aks install-cli
```

Mit dem Befehl [az aks get-credentials](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-get-credentials) können Sie `kubectl` für die Verbindungsherstellung mit Ihrem Kubernetes-Cluster konfigurieren. Mit diesem Befehl werden die Anmeldeinformationen heruntergeladen, und die Kubernetes-Befehlszeilenschnittstelle wird für deren Verwendung konfiguriert.

```azurecli-interactive
az aks get-credentials --resource-group wordpress-project --name wordpresscluster
```

> [!NOTE]
> Der obige Befehl verwendet den Standardspeicherort für die [Kubernetes-Konfigurationsdatei](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/), d. h. `~/.kube/config`. Sie können mit *--file* einen anderen Speicherort für Ihre Kubernetes-Konfigurationsdatei angeben.

Überprüfen Sie die Verbindung mit Ihrem Cluster mithilfe des Befehls [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get), um eine Liste der Clusterknoten zurückzugeben.

```azurecli-interactive
kubectl get nodes
```

Die folgende Beispielausgabe zeigt den in den vorherigen Schritten erstellten Knoten. Vergewissern Sie sich, dass der Knoten den Status *Bereit* hat:

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
```

## <a name="create-an-azure-database-for-mysql---flexible-server"></a>Erstellen einer Instanz von Azure Database for MySQL – Flexible Server
Erstellen Sie mithilfe des Befehls [az mysql flexible-server create](/cli/azure/mysql/flexible-server?view=azure-cli-latest&preserve-view=true) einen flexiblen Server. Mit dem folgenden Befehl wird ein Server mit Dienststandards und -werten aus dem lokalen Kontext Ihrer Azure CLI erstellt:

```azurecli-interactive
az mysql flexible-server create --public-access <YOUR-IP-ADDRESS>
```

Der erstellte Server weist die folgenden Attribute auf:
- Bei der erstmaligen Bereitstellung des Servers wird eine neue leere Datenbank (```flexibleserverdb```) erstellt. In dieser Schnellstartanleitung wird diese Datenbank verwendet.
- Er besitzt automatisch generierte Werte für Servername, Administratorbenutzername, Administratorkennwort und Ressourcengruppenname (sofern nicht bereits im lokalen Kontext angegeben) und befindet sich am gleichen Standort wie die Ressourcengruppe.
- Die Dienststandardwerte für verbleibende Serverkonfigurationen lauten wie folgt: Computetarif (Burstable) (Burstfähig), Computegröße/SKU (B1MS), Aufbewahrungszeitraum für Sicherungen (7 Tage) und MySQL-Version (5.7).
- Die Verwendung des Arguments „public-access“ ermöglicht die Erstellung eines durch Firewallregeln geschützten Servers mit öffentlichem Zugriff. Geben Sie Ihre IP-Adresse an, damit sie der Firewallregel hinzugefügt werden kann, um den Zugriff von Ihrem Clientcomputer aus zu ermöglichen.
- Da in dem Befehl der lokale Kontext verwendet wird, wird der Server in der Ressourcengruppe ```wordpress-project``` und in der Region ```eastus``` erstellt.


## <a name="build-your-wordpress-docker-image"></a>Erstellen Ihres WordPress-Docker-Images

Laden Sie die [aktuelle WordPress-Version](https://wordpress.org/download/) herunter. Erstellen Sie für Ihr Projekt ein neues Verzeichnis namens ```my-wordpress-app```, und verwenden Sie die folgende einfache Ordnerstruktur:

```
└───my-wordpress-app
    └───public
        ├───wp-admin
        │   ├───css
        . . . . . . .
        ├───wp-content
        │   ├───plugins
          . . . . . . .
        └───wp-includes
        . . . . . . .
        ├───wp-config-sample.php
        ├───index.php
        . . . . . . .
    └─── Dockerfile

```


Benennen Sie ```wp-config-sample.php``` in ```wp-config.php``` um, und ersetzen Sie die Zeilen 21 bis 32 durch den im Anschluss bereitgestellten Codeausschnitt. Der folgende Codeausschnitt liest Datenbankhost, Benutzername und Kennwort aus der Kubernetes-Manifestdatei:

```php
//Using environment variables for DB connection information

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */

$connectstr_dbhost = getenv('DATABASE_HOST');
$connectstr_dbusername = getenv('DATABASE_USERNAME');
$connectstr_dbpassword = getenv('DATABASE_PASSWORD');

/** MySQL database name */
define('DB_NAME', 'flexibleserverdb');

/** MySQL database username */
define('DB_USER', $connectstr_dbusername);

/** MySQL database password */
define('DB_PASSWORD',$connectstr_dbpassword);

/** MySQL hostname */
define('DB_HOST', $connectstr_dbhost);

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/** SSL*/
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
```

Erstellen Sie ein neues Dockerfile, und kopieren Sie den im Anschluss bereitgestellten Codeausschnitt. Dieses Dockerfile dient zum Einrichten von Apache-Webserver mit PHP sowie zum Aktivieren der mysqli-Erweiterung:

```docker
FROM php:7.2-apache
COPY public/ /var/www/html/
RUN docker-php-ext-install mysqli
RUN docker-php-ext-enable mysqli
```

## <a name="build-your-docker-image"></a>Erstellen Ihres Docker-Images
Stellen Sie in einem Terminal mithilfe des Befehls ```cd``` sicher, dass Sie sich im Verzeichnis ```my-wordpress-app``` befinden. Führen Sie den folgenden Befehl aus, um Ihr Bulletin Board-Image zu erstellen:

``` bash

docker build --tag myblog:latest . 

```

Stellen Sie Ihr Image in [Docker Hub](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) oder in [Azure Container Registry](../../container-registry/container-registry-get-started-azure-cli.md) bereit.

> [!IMPORTANT]
>Führen Sie bei Verwendung von Azure Container Registry (ACR) den Befehl ```az aks update``` aus, um das ACR-Konto mit dem AKS-Cluster zu verknüpfen.
>
>```azurecli-interactive
>az aks update -n myAKSCluster -g wordpress-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>Erstellen einer Kubernetes-Manifestdatei

Eine Kubernetes-Manifestdatei definiert einen gewünschten Zustand (Desired State) für den Cluster – also beispielsweise, welche Containerimages ausgeführt werden sollen. Erstellen Sie eine Manifestdatei namens `mywordpress.yaml`, und fügen Sie die folgende YAML-Definition ein.

>[!IMPORTANT]
> - Ersetzen Sie ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]``` durch den Namen und das Tag Ihres tatsächlichen WordPress-Docker-Images (beispielsweise ```docker-hub-user/myblog:latest```).
> - Aktualisieren Sie den Abschnitt ```env``` mit Ihren Werten für ```SERVERNAME```, ```YOUR-DATABASE-USERNAME``` und ```YOUR-DATABASE-PASSWORD``` Ihres flexiblen MySQL-Servers.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress-blog
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wordpress-blog
  template:
    metadata:
      labels:
        app: wordpress-blog
    spec:
      containers:
      - name: wordpress-blog
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.mysql.database.azure.com"
        - name: DATABASE_USERNAME
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "flexibleserverdb"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - wordpress-blog
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: php-svc
spec:
  type: LoadBalancer
  ports:
    - port: 80
  selector:
    app: wordpress-blog
```

## <a name="deploy-wordpress-to-aks-cluster"></a>Bereitstellen von WordPress im AKS-Cluster
Stellen Sie die Anwendung über den Befehl [kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) bereit, und geben Sie den Namen Ihres YAML-Manifests an:

```console
kubectl apply -f mywordpress.yaml
```

In der folgenden Beispielausgabe sind die erfolgreich erstellten Bereitstellungen und Dienste aufgeführt:

```output
deployment "wordpress-blog" created
service "php-svc" created
deployment "azure-vote-front" created
service "php-svc" created
```

## <a name="test-the-application"></a>Testen der Anwendung

Wenn die Anwendung ausgeführt wird, macht ein Kubernetes-Dienst das Anwendungs-Front-End im Internet verfügbar. Dieser Vorgang kann einige Minuten dauern.

Verwenden Sie zum Überwachen des Fortschritts den Befehl [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) mit dem Argument `--watch`.

```azurecli-interactive
kubectl get service wordpress-blog --watch
```

Die externe IP-Adresse (*EXTERNAL-IP*) für den Dienst *wordpress-blog* wird zunächst als *pending* (ausstehend) angezeigt.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
wordpress-blog   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Sobald die externe IP-Adresse (*EXTERNAL-IP*) von pending (*ausstehend*) in eine tatsächliche öffentliche IP-Adresse geändert wurde, verwenden Sie `CTRL-C`, um die `kubectl`-Überwachung zu beenden. Die folgende Beispielausgabe zeigt eine gültige öffentliche IP-Adresse, die dem Dienst zugewiesen ist:

```output
wordpress-blog  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

### <a name="browse-wordpress"></a>Durchsuchen von WordPress

Öffnen Sie die externe IP-Adresse Ihres Diensts in einem Webbrowser, um Ihre WordPress-Installationsseite anzuzeigen:

   :::image type="content" source="./media/tutorial-deploy-wordpress-on-aks/wordpress-aks-installed-success.png" alt-text="Erfolgreiche WordPress-Installation in AKS und auf dem flexiblen MySQL-Server":::

>[!NOTE]
> - Aktuell wird von der WordPress-Website kein HTTPS verwendet. Es empfiehlt sich, [TLS mit Ihren eigenen Zertifikaten zu aktivieren](../../aks/ingress-own-tls.md).
> - Sie können [HTTP-Routing](../../aks/http-application-routing.md) für Ihren Cluster aktivieren.

## <a name="clean-up-the-resources"></a>Bereinigen der Ressourcen

Zum Vermeiden von Azure-Gebühren sollten Sie nicht benötigte Ressourcen bereinigen.  Wenn der Cluster nicht mehr benötigt wird, entfernen Sie mit dem Befehl [az group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete) die Ressourcengruppe, den Containerdienst und alle zugehörigen Ressourcen.

```azurecli-interactive
az group delete --name wordpress-project --yes --no-wait
```

> [!NOTE]
> Wenn Sie den Cluster löschen, wird der vom AKS-Cluster verwendete Azure Active Directory-Dienstprinzipal nicht entfernt. Schritte zum Entfernen des Dienstprinzipals finden Sie unter den [Überlegungen zum AKS-Dienstprinzipal und dessen Löschung](../../aks/kubernetes-service-principal.md#additional-considerations). Wenn Sie eine verwaltete Identität verwendet haben, wird die Identität von der Plattform verwaltet und muss nicht entfernt werden.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich, wie Sie [auf das Kubernetes-Webdashboard für Ihren AKS-Cluster zugreifen](../../aks/kubernetes-dashboard.md).
- Machen Sie sich mit der Vorgehensweise zum [Skalieren Ihres Clusters](../../aks/tutorial-kubernetes-scale.md) vertraut.
- Erfahren Sie, wie Sie Ihren [flexiblen MySQL-Server](./quickstart-create-server-cli.md) verwalten.
- Informieren Sie sich über das [Konfigurieren von Serverparametern](./how-to-configure-server-parameters-cli.md) für Ihren Datenbankserver.

