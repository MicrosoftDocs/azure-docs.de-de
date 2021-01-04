---
title: Ausführen eines Apache Spark-Auftrags mit Azure Kubernetes Service (AKS)
description: Verwenden Sie Azure Kubernetes Service (AKS), um einen Apache Spark-Auftrag für umfangreiche Datenverarbeitungen zu erstellen und auszuführen.
author: lenadroid
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: alehall
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: e94d9d586f15c228436a8a9d48fb12c26369c449
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92742350"
---
# <a name="running-apache-spark-jobs-on-aks"></a>Ausführen von Apache Spark-Aufträgen in ACS

[Apache Spark][apache-spark] ist eine schnelle Engine zur Verarbeitung von umfangreichen Daten. Ab dem [Spark 2.3.0-Release][spark-kubernetes-earliest-version] unterstützt Apache Spark die native Integration in Kubernetes-Cluster. Azure Kubernetes Service (AKS) ist eine verwaltete Kubernetes-Umgebung, die in Azure ausgeführt wird. In diesem Dokument werden die Vorbereitung und Ausführung von Apache Spark-Aufträgen in einem Azure Kubernetes Service-Cluster (AKS) ausführlich beschrieben.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um die Schritte in diesem Artikel ausführen zu können.

* Grundlegende Kenntnisse von Kubernetes und [Apache Spark][spark-quickstart]
* [Docker-Hub][docker-hub]-Konto oder [Azure Container Registry][acr-create]
* Eine Installation der [Azure CLI][azure-cli] auf Ihrem Entwicklungssystem
* Eine [JDK 8][java-install]-Installation auf Ihrem System
* [Apache Maven][maven-install] muss auf Ihrem System installiert sein.
* Eine SBT-Installation ([Scala Build Tool][sbt-install]) auf Ihrem System
* Auf Ihrem System installierte Git-Befehlszeilentools

## <a name="create-an-aks-cluster"></a>Erstellen eines AKS-Clusters

Spark dient zur Verarbeitung von umfangreichen Daten und erfordert die Größenanpassung von Kubernetes-Knoten gemäß den Spark-Ressourcenanforderungen. Wir empfehlen eine Mindestgröße von `Standard_D3_v2` für Azure Kubernetes Service-Knoten (AKS).

Wenn Sie einen ACS-Cluster benötigen, der diese Empfehlung zu den Mindestanforderungen erfüllt, führen Sie die folgenden Befehle aus.

Erstellen Sie eine Ressourcengruppe für den Cluster.

```azurecli
az group create --name mySparkCluster --location eastus
```

Erstellen Sie einen Dienstprinzipals für den Cluster. Nach der Erstellung benötigen Sie die AppID und das Kennwort des Dienstprinzipals für den nächsten Befehl.

```azurecli
az ad sp create-for-rbac --name SparkSP
```

Erstellen Sie den AKS-Cluster mit Knoten der Größe `Standard_D3_v2` und Werten für „appId“ und „password“, die als service-principal- und client-secret-Parameter übergeben werden.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2 --generate-ssh-keys --service-principal <APPID> --client-secret <PASSWORD>
```

Stellen Sie eine Verbindung mit dem ACS-Cluster her.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Wenn Sie Azure Container Registry (ACR) zum Speichern von Containerimages verwenden, konfigurieren Sie die Authentifizierung zwischen ACS und ACR. Informationen zu den entsprechenden Schritten finden Sie in der [Dokumentation zur Authentifizierung mit ACR][acr-aks].

## <a name="build-the-spark-source"></a>Erstellen der Spark-Quelle

Vor der Ausführung von Spark-Aufträgen in einem ACS-Cluster müssen Sie den Spark-Quellcode erstellen und in einem Containerimage packen. Die Spark-Quelle umfasst Skripts, die zur Durchführung dieses Vorgangs verwendet werden können.

Klonen Sie das Spark-Projektrepository auf Ihrem Entwicklungssystem.

```bash
git clone -b branch-2.4 https://github.com/apache/spark
```

Ändern Sie es in das Verzeichnis des geklonten Repositorys, und speichern Sie den Pfad der Spark-Quelle in einer Variablen.

```bash
cd spark
sparkdir=$(pwd)
```

Wenn Sie mehrere JDK-Versionen installiert haben, legen Sie `JAVA_HOME` fest, um Version 8 für die aktuelle Sitzung zu verwenden.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Führen Sie den folgenden Befehl zum Erstellen des Spark-Quellcodes mit Kubernetes-Unterstützung aus.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

Die folgenden Befehle erstellen die Spark-Containerimages und übertragen diese auf eine Containerimageregistrierung. Ersetzen Sie `registry.example.com` mit dem Namen Ihrer Containerregistrierung und `v1` mit dem Tag, das Sie verwenden möchten. Wenn Sie Docker Hub verwenden, ist dieser Wert der Name der Registrierung. Wenn Sie Azure Container Registry (ACR) verwenden, ist dieser Wert der Name des ACR-Anmeldeservers.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

Übertragen Sie ein Image mithilfe von Push an Ihre Containerimageregistrierung.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Vorbereiten eines Spark-Auftrags

Bereiten Sie als Nächstes einen Spark-Auftrag vor. Eine JAR-Datei wird zum Speichern des Spark-Auftrags verwendet und ist für die Ausführung des `spark-submit`-Befehls erforderlich. Die JAR-Datei kann über eine öffentliche URL zugänglich gemacht oder bereits vorab in ein Containerimage gepackt werden. In diesem Beispiel wird eine JAR-Beispieldatei erstellt, um den Wert von Pi zu berechnen. Diese JAR-Datei wird dann in Azure Storage hochgeladen. Wenn Sie bereits über eine JAR-Datei verfügen, können Sie diese auf Wunsch ersetzen.

Erstellen Sie ein Verzeichnis, in dem das Projekt für einen Spark-Auftrag erstellt werden soll.

```bash
mkdir myprojects
cd myprojects
```

Erstellen Sie anhand einer Vorlage ein neues Scala-Projekt.

```bash
sbt new sbt/scala-seed.g8
```

Wenn Sie dazu aufgefordert werden, geben Sie `SparkPi` für den Projektnamen ein.

```bash
name [Scala Seed Project]: SparkPi
```

Navigieren Sie zu dem neu erstellten Projektverzeichnis.

```bash
cd sparkpi
```

Führen Sie die folgenden Befehle zum Hinzufügen eines SBT-Plug-Ins aus, die das Packen des Projekts als JAR-Datei ermöglicht.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.10")' >> project/assembly.sbt
```

Führen Sie diese Befehle zum Kopieren des Beispielcodes in das neu erstellte Projekt aus, und fügen Sie alle erforderlichen Abhängigkeiten hinzu.

```bash
EXAMPLESDIR="src/main/scala/org/apache/spark/examples"
mkdir -p $EXAMPLESDIR
cp $sparkdir/examples/$EXAMPLESDIR/SparkPi.scala $EXAMPLESDIR/SparkPi.scala

cat <<EOT >> build.sbt
// https://mvnrepository.com/artifact/org.apache.spark/spark-sql
libraryDependencies += "org.apache.spark" %% "spark-sql" % "2.3.0" % "provided"
EOT

sed -ie 's/scalaVersion.*/scalaVersion := "2.11.11"/' build.sbt
sed -ie 's/name.*/name := "SparkPi",/' build.sbt
```

Führen Sie den folgenden Befehl aus, um das Projekt in eine JAR-Datei zu packen.

```bash
sbt assembly
```

Nachdem diese gepackt wurde, sollten Sie eine Ausgabe ähnlich der folgenden erhalten:

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>Kopieren eines Auftrags in Storage

Erstellen Sie ein Azure Storage-Konto und einen Container, in dem die JAR-Datei gespeichert werden soll.

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location eastus
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

Laden Sie mit den folgenden Befehlen die JAR-Datei in das Azure Storage-Konto hoch.

```azurecli
CONTAINER_NAME=jars
BLOB_NAME=SparkPi-assembly-0.1.0-SNAPSHOT.jar
FILE_TO_UPLOAD=target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar

echo "Creating the container..."
az storage container create --name $CONTAINER_NAME
az storage container set-permission --name $CONTAINER_NAME --public-access blob

echo "Uploading the file..."
az storage blob upload --container-name $CONTAINER_NAME --file $FILE_TO_UPLOAD --name $BLOB_NAME

jarUrl=$(az storage blob url --container-name $CONTAINER_NAME --name $BLOB_NAME | tr -d '"')
```

Die Variable `jarUrl` enthält nun den öffentlich zugänglichen Pfad zur JAR-Datei.

## <a name="submit-a-spark-job"></a>Übermitteln eines Spark-Auftrags

Starten Sie Kube-Proxy in einer separaten Befehlszeile mit folgendem Code.

```bash
kubectl proxy
```

Navigieren Sie zurück zum Stammverzeichnis des Spark-Repositorys.

```bash
cd $sparkdir
```

Erstellen Sie ein Dienstkonto, das über ausreichende Berechtigungen zum Ausführen eines Auftrags verfügt.

```bash
kubectl create serviceaccount spark
kubectl create clusterrolebinding spark-role --clusterrole=edit --serviceaccount=default:spark --namespace=default
```

Übermitteln Sie den Auftrag mithilfe von `spark-submit`.

```bash
./bin/spark-submit \
  --master k8s://http://127.0.0.1:8001 \
  --deploy-mode cluster \
  --name spark-pi \
  --class org.apache.spark.examples.SparkPi \
  --conf spark.executor.instances=3 \
  --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
  --conf spark.kubernetes.container.image=$REGISTRY_NAME/spark:$REGISTRY_TAG \
  $jarUrl
```

Dieser Vorgang startet den Spark-Auftrag, der den Auftragsstatus für Ihre Shellsitzung streamt. Während der Auftragsausführung können Sie den Spark-Treiberpod und die Executorpods mithilfe des Befehls „kubectl get pods“ anzeigen. Öffnen Sie eine zweite Terminalsitzung, um diese Befehle auszuführen.

```console
kubectl get pods
```

```output
NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

Während der Auftragsausführung können Sie auch auf die Spark-Benutzeroberfläche zugreifen. Verwenden Sie in der zweiten Terminalsitzung den Befehl `kubectl port-forward`, um Zugriff auf die Spark-Benutzeroberfläche zu bieten.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Rufen Sie für den Zugriff auf die Spark-Benutzeroberfläche die Adresse `127.0.0.1:4040` in einem Browser auf.

![Spark-Benutzeroberfläche](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>Abrufen der Auftragsergebnisse und -protokolle

Nach Abschluss des Auftrags wechselt der Treiberpod in den Zustand „Abgeschlossen“. Rufen Sie mithilfe des folgenden Befehls den Namen des Pods ab.

```bash
kubectl get pods --show-all
```

Ausgabe:

```output
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

Verwenden Sie den Befehl `kubectl logs`, um Protokolle aus dem Spark-Treiberpod abzurufen. Ersetzen Sie den Podnamen durch den Namen Ihres Treiberpods.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

In diesen Protokollen können Sie das Ergebnis des Spark-Auftrags sehen, also den Wert von Pi.

```output
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>Packen einer JAR-Datei mit dem Containerimage

Im obigen Beispiel wurde die Spark-JAR-Datei in Azure Storage hochgeladen. Eine weitere Option ist das Packen der JAR-Datei in benutzerdefinierte Docker-Images.

Suchen Sie hierfür `dockerfile` für das Spark-Image im Verzeichnis `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/`. Fügen Sie an einer beliebigen Stellen zwischen den Deklarationen `WORKDIR` und `ENTRYPOINT` eine `ADD`-Anweisung für den Spark-Auftrag `jar` hinzu.

Aktualisieren Sie den JAR-Pfad zum Speicherort der Datei `SparkPi-assembly-0.1.0-SNAPSHOT.jar` auf Ihrem Entwicklungssystem. Sie können auch eine eigene benutzerdefinierte JAR-Datei verwenden.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Erstellen Sie das Image mit den enthaltenen Spark-Skripts, und übertragen Sie es mithilfe von Push.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

Bei der Ausführung des Auftrags kann anstelle der Remote-JAR-URL das `local://`-Schema mit dem Pfad zur JAR-Datei im Docker-Image verwendet werden.

```bash
./bin/spark-submit \
    --master k8s://https://<k8s-apiserver-host>:<k8s-apiserver-port> \
    --deploy-mode cluster \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --conf spark.executor.instances=3 \
    --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
    --conf spark.kubernetes.container.image=<spark-image> \
    local:///opt/spark/work-dir/<your-jar-name>.jar
```

> [!WARNING]
> Aus der Spark-[Dokumentation][spark-docs]: „The Kubernetes scheduler is currently experimental. In future versions, there may be behavioral changes around configuration, container images and entrypoints.“ (Der Kubernetes-Planer ist derzeit experimentell. Zukünftige Versionen könnten Verhaltensänderungen bezüglich Konfiguration, Containerimages und Einstiegspunkten aufweisen.)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in der Spark-Dokumentation.

> [!div class="nextstepaction"]
> [Spark-Dokumentation][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: /azure/developer/java/fundamentals/java-jdk-long-term-support
[maven-install]: https://maven.apache.org/install.html
[sbt-install]: https://www.scala-sbt.org/1.0/docs/Setup.html
[spark-docs]: https://spark.apache.org/docs/latest/running-on-kubernetes.html
[spark-kubernetes-earliest-version]: https://spark.apache.org/releases/spark-release-2-3-0.html
[spark-quickstart]: https://spark.apache.org/docs/latest/quick-start.html


<!-- LINKS - internal -->
[acr-aks]: cluster-container-registry-integration.md
[acr-create]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-quickstart]: ./index.yml
[azure-cli]: /cli/azure/?view=azure-cli-latest
[storage-account]: ../storage/blobs/storage-quickstart-blobs-cli.md
