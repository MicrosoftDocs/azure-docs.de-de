---
title: Erstellen einer Azure Service Fabric-Containeranwendung unter Linux
description: Erstellen Sie Ihre erste Linux-Containeranwendung unter Azure Service Fabric. Erstellen Sie ein Docker-Image mit Ihrer Anwendung, übertragen Sie es per Push an eine Containerregistrierung, erstellen Sie eine Service Fabric-Containeranwendung, und stellen Sie diese bereit.
ms.topic: conceptual
ms.date: 1/4/2019
ms.custom: devx-track-python
ms.openlocfilehash: 0481cc2d36f7882bbd8eea9b984c3dc388de5dee
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96534079"
---
# <a name="create-your-first-service-fabric-container-application-on-linux"></a>Erstellen Ihrer ersten Service Fabric-Containeranwendung unter Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

Zum Ausführen einer vorhandenen Anwendung eines Linux-Containers in einem Service Fabric-Cluster sind keine Änderungen an Ihrer Anwendung erforderlich. In diesem Artikel wird schrittweise beschrieben, wie Sie ein Docker-Image mit einer Python [Flask](http://flask.pocoo.org/)-Webanwendung erstellen und in einem Service Fabric-Cluster bereitstellen. Außerdem stellen Sie Ihre Containeranwendung per [Azure Container Registry](../container-registry/index.yml) bereit. In diesem Artikel werden grundlegende Kenntnisse von Docker vorausgesetzt. Weitere Informationen zu Docker finden Sie in der [Docker-Übersicht](https://docs.docker.com/engine/understanding-docker/).

> [!NOTE]
> Dieser Artikel bezieht sich auf eine Linux-Entwicklungsumgebung.  Die Service Fabric-Clusterruntime und die Docker-Runtime müssen unter dem gleichen Betriebssystem ausgeführt werden.  Sie können Linux-Container nicht in einem Windows-Cluster ausführen.

## <a name="prerequisites"></a>Voraussetzungen
* Ein Entwicklungscomputer, auf dem Folgendes ausgeführt wird:
  * [Service Fabric-SDK und -Tools](service-fabric-get-started-linux.md)
  * [Docker CE für Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Service Fabric-Befehlszeilenschnittstelle](service-fabric-cli.md)

* Einen Linux-Cluster mit drei oder mehr Knoten.

* Eine Registrierung in Azure Container Registry – erstellen Sie in Ihrem Azure-Abonnement eine [Containerregistrierung](../container-registry/container-registry-get-started-portal.md). 

## <a name="define-the-docker-container"></a>Definieren des Docker-Containers
Erstellen Sie ein Image auf Grundlage des [Python-Image](https://hub.docker.com/_/python/) in Docker Hub. 

Geben Sie Ihren Docker-Container in einer Dockerfile-Datei an. Die Dockerfile-Datei enthält Anweisungen für die Einrichtung der Umgebung in Ihrem Container, für das Laden der auszuführenden Anwendung und für die Portzuordnung. Die Dockerfile-Datei ist die Eingabe für den Befehl `docker build`, der das Image erstellt. 

Erstellen Sie ein leeres Verzeichnis, und erstellen Sie die Datei *Dockerfile* (ohne Dateierweiterung). Fügen Sie der *Dockerfile* Folgendes hinzu, und speichern Sie Ihre Änderungen:

```
# Use an official Python runtime as a base image
FROM python:2.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Weitere Informationen finden Sie in der [Dockerfile-Referenz](https://docs.docker.com/engine/reference/builder/).

## <a name="create-a-basic-web-application"></a>Erstellen einer einfachen Webanwendung
Erstellen Sie eine Flask-Webanwendung, die über Port 80 lauscht und „Hello World!“ zurückgibt. Erstellen Sie in demselben Verzeichnis die Datei *requirements.txt*. Fügen Sie Folgendes hinzu, und speichern Sie die Änderungen:
```
Flask
```

Erstellen Sie auch die Datei *app.py*, und fügen Sie den folgenden Codeausschnitt hinzu:

```python
from flask import Flask

app = Flask(__name__)


@app.route("/")
def hello():

    return 'Hello World!'


if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

## <a name="login-to-docker-and-build-the-image"></a>Anmelden bei Docker und Erstellen des Images

Nun erstellen Sie das Image, mit dem Ihre Webanwendung ausgeführt wird. Beim Pullen von öffentlichen Images aus Docker (wie `python:2.7-slim` in unserer Dockerfile-Datei) besteht eine bewährte Methode darin, sich mit Ihrem Docker Hub-Konto zu authentifizieren, anstatt einen anonymen Pull Request zu erstellen.

> [!NOTE]
> Beim Ausführen von häufigen anonymen Pull Requests wird möglicherweise ein Docker-Fehler wie `ERROR: toomanyrequests: Too Many Requests.` oder `You have reached your pull rate limit.` angezeigt. Authentifizieren Sie sich bei Docker Hub, um diese Fehler zu verhindern. Weitere Informationen finden Sie unter [Verwalten öffentlicher Inhalte mit Azure Container Registry](../container-registry/buffer-gate-public-content.md).

Öffnen Sie ein PowerShell-Fenster, und navigieren Sie zu dem Verzeichnis, das die Dockerfile-Datei enthält. Führen Sie anschließend die folgenden Befehle aus:

```
docker login
docker build -t helloworldapp .
```

Dieser Befehl erstellt das neue Image gemäß den Anweisungen aus der Dockerfile-Datei. Das Image erhält den Namen `helloworldapp`. Hierzu wird der Parameter „-t“ (für Tagging) verwendet. Zur Erstellung eines Containerimages wird zunächst das Basisimage von Docker Hub heruntergeladen, und die Anwendung wird hinzugefügt. 

Wenn der Buildbefehl abgeschlossen ist, rufen Sie den Befehl `docker images` ab, um Informationen zum neuen Image anzuzeigen:

```bash
$ docker images
    
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              86838648aab6        2 minutes ago       194 MB
```

## <a name="run-the-application-locally"></a>Lokales Ausführen der Anwendung
Stellen Sie sicher, dass Ihre Containeranwendung lokal ausgeführt wird, bevor Sie sie an die Containerregistrierung übertragen. 

Führen Sie die Anwendung aus, und ordnen Sie den Port 4000 Ihres Computers dem verfügbar gemachten Port 80 des Containers zu:

```bash
docker run -d -p 4000:80 --name my-web-site helloworldapp
```

*name* vergibt einen Namen für den ausgeführten Container (anstelle der Container-ID).

Stellen Sie eine Verbindung mit dem ausgeführten Container her. Öffnen Sie einen Webbrowser, und verweisen Sie auf die IP-Adresse, die über Port 4000 zurückgegeben wurde, z.B. „http:\//localhost:4000“. Die Überschrift „Hello World!“ wird im Browser angezeigt.

![Hello World!][hello-world]

Führen Sie Folgendes aus, um den Container zu beenden:

```bash
docker stop my-web-site
```

Löschen Sie den Container von Ihrem Entwicklungscomputer:

```bash
docker rm my-web-site
```

## <a name="push-the-image-to-the-container-registry"></a>Übertragen des Images an die Containerregistrierung mithilfe von Push
Nachdem Sie sichergestellt haben, dass die Anwendung in Docker ausgeführt wird, können Sie das Image per Pushvorgang in Ihre Registrierung in der Azure Container Registry übertragen.

Führen Sie `docker login` aus, um sich mit Ihren [Registrierungsanmeldeinformationen](../container-registry/container-registry-authentication.md) bei der Containerregistrierung anzumelden.

Im folgenden Beispiel werden die ID und das Kennwort eines Azure Active Directory-[Dienstprinzipals](../active-directory/develop/app-objects-and-service-principals.md) übergeben. Angenommen, Sie haben Ihrer Registrierung für ein Automatisierungsszenario einen Dienstprinzipal zugewiesen. Alternativ können Sie sich mit Ihrem für die Registrierung verwendeten Benutzernamen und Kennwort anmelden.

```bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Mit dem folgenden Befehl wird ein Tag oder Alias des Images mit einem vollqualifizierten Pfad zur Registrierung erstellt. In diesem Beispiel wird das Image im `samples`-Namespace platziert, um den Stamm der Registrierung nicht zu überladen.

```bash
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Übertragen des Image mithilfe von Push an Ihre Containerregistrierung:

```bash
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="package-the-docker-image-with-yeoman"></a>Packen des Docker-Image mit Yeoman
Das Service Fabric SDK für Linux enthält einen [Yeoman](https://yeoman.io/)-Generator, mit dem Sie problemlos die Anwendung erstellen und ein Containerimage hinzufügen können. Lassen Sie uns mit Yeoman eine Anwendung mit einem einzelnen Docker-Container erstellen, deren Name *SimpleContainerApp* lautet.

Öffnen Sie zum Erstellen einer Service Fabric-Containeranwendung ein Terminalfenster, und führen Sie `yo azuresfcontainer` aus. 

Legen Sie einen Namen für Ihre Anwendung (beispielsweise `mycontainer`) und einen Namen für den Anwendungsdienst (beispielsweise `myservice`) fest.

Geben Sie als Imagename die URL für das Containerimage in einer Containerregistrierung an (Beispiel: myregistry.azurecr.io/samples/helloworldapp). 

Da für dieses Image ein Workloadeinstiegspunkt definiert ist, müssen Sie nicht explizit Eingabebefehle angeben. (Befehle werden im Container ausgeführt, wodurch der Container nach dem Start weiter ausgeführt wird.) 

Geben Sie als Instanzanzahl „1“ an.

Geben Sie die Portzuordnung im entsprechenden Format an. Im Falle dieses Artikels ist das ```80:4000```. So haben Sie Folgendes konfiguriert: Alle Anfragen, die auf dem Hostcomputer unter Port 4000 eingehen, werden an Port 80 auf dem Container umgeleitet.

![Service Fabric-Yeoman-Generator für Container][sf-yeoman]

## <a name="configure-container-repository-authentication"></a>Konfigurieren der Authentifizierung des Containerrepositorys

Informationen dazu, wie Sie verschiedene Authentifizierungstypen für das Herunterladen von Containerimages konfigurieren, finden Sie unter [Authentifizierung für Containerrepositorys](configure-container-repository-credentials.md).

## <a name="configure-isolation-mode"></a>Isolationsmodus konfigurieren
In der Runtimeversion 6.3 wird die VM-Isolierung für Linux-Container unterstützt, wodurch zwei Isolationsmodi für Container unterstützt werden: „process“ und „Hyper-V“. Beim Isolationsmodus „Hyper-V“ werden die Kernel der einzelnen Container und des Containerhosts voneinander getrennt. Der Isolationsmodus „Hyper-V“ wird mithilfe von [Clear Containers](https://software.intel.com/en-us/articles/intel-clear-containers-2-using-clear-containers-with-docker) implementiert. Der Isolationsmodus für Linux-Cluster wird im Element `ServicePackageContainerPolicy` der Anwendungsmanifestdatei angegeben. Als Isolationsmodi können `process`, `hyperv` und `default` angegeben werden. Der Standardisolationsmodus ist „process“. Im folgenden Codeausschnitt wird gezeigt, wie der Isolationsmodus in der Anwendungsmanifestdatei angegeben wird.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0"/>
      <Policies>
        <ServicePackageContainerPolicy Hostname="votefront" Isolation="hyperv">
          <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
        </ServicePackageContainerPolicy>
    </Policies>
  </ServiceManifestImport>
```


## <a name="configure-resource-governance"></a>Konfigurieren der Ressourcenkontrolle
Die [Ressourcenkontrolle](service-fabric-resource-governance.md) beschränkt die Ressourcen, die vom Container auf dem Host verwendet werden können. Mit dem `ResourceGovernancePolicy`-Element, das im Anwendungsmanifest angegeben ist, werden Ressourceneinschränkungen für ein Dienstcodepaket deklariert. Ressourcenlimits können für die folgenden Ressourcen festgelegt werden: Arbeitsspeicher, MemorySwap, CpuShares (relative CPU-Gewichtung), MemoryReservationInMB, BlkioWeight (relative BlockIO-Gewichtung). In diesem Beispiel erhält das Dienstpaket „Guest1Pkg“ einen Kern auf den Clusterknoten, auf denen es platziert wurde. Die Einschränkungen des Arbeitsspeichers sind absolut, daher ist das Codepaket auf 1024 MB Arbeitsspeicher (sowie eine weiche Reservierungsgarantie desselben) beschränkt. Codepakete (Container oder Prozesse) können nicht mehr Arbeitsspeicher zuzuweisen, als dieser Grenzwert zulässt, ein entsprechender Versuch führt daher zu einer Ausnahme „Nicht genügend Arbeitsspeicher“. Damit die Erzwingung des Ressourcenlimits funktioniert, sollten für alle Codepakete innerhalb eines Dienstpakets Arbeitsspeicherlimits festgelegt sein.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="MyServicePKg" ServiceManifestVersion="1.0.0" />
  <Policies>
    <ServicePackageResourceGovernancePolicy CpuCores="1"/>
    <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
  </Policies>
</ServiceManifestImport>
```




## <a name="configure-docker-healthcheck"></a>Konfigurieren von „docker HEALTHCHECK“ 

Beim Starten von Version 6.1 integriert Service Fabric automatisch [docker HEALTHCHECK](https://docs.docker.com/engine/reference/builder/#healthcheck)-Ereignisse in seinen Bericht zur Systemintegrität. Wenn für Ihren Container **HEALTHCHECK** aktiviert ist, bedeutet dies Folgendes: Von Service Fabric wird die Dienstintegrität immer dann gemeldet, wenn sich der Integritätsstatus des Containers gemäß Meldung durch Docker ändert. Die Integritätsmeldung **OK** wird in [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) angezeigt, wenn für *health_status* die Meldung *healthy* erfolgt, und **WARNING**, wenn für *health_status* die Meldung *unhealthy* erfolgt. 

Ab dem neuesten Aktualisierungsrelease v6.4 können Sie festlegen, dass Docker-HEALTHCHECK-Auswertungen als Fehler gemeldet werden. Wenn diese Option aktiviert ist, wird die Integritätsmeldung **OK** angezeigt, wenn *health_status* als *healthy* gemeldet wird, bzw. **ERROR**, wenn *health_status* als *unhealthy* gemeldet wird.

Die Anweisung **HEALTHCHECK**, die auf die tatsächliche Prüfung zur Überwachung der Containerintegrität verweist, muss in der Dockerfile-Datei enthalten sein, die beim Generieren des Containerimages verwendet wurde.

![Screenshot mit Details des bereitgestellten Dienstpakets „NodeServicePackage“][1]

![HealthCheckUnhealthyApp][2]

![HealthCheckUnhealthyDsp][3]

Sie können das **HEALTHCHECK**-Verhalten für jeden Container konfigurieren, indem Sie **HealthConfig**-Optionen im Rahmen von **ContainerHostPolicies** im Anwendungsmanifest angeben.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="ContainerServicePkg" ServiceManifestVersion="2.0.0" />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <HealthConfig IncludeDockerHealthStatusInSystemHealthReport="true"
              RestartContainerOnUnhealthyDockerHealthStatus="false" 
              TreatContainerUnhealthyStatusAsError="false" />
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
```
*IncludeDockerHealthStatusInSystemHealthReport* ist standardmäßig auf **true**, *RestartContainerOnUnhealthyDockerHealthStatus* auf **false** und *TreatContainerUnhealthyStatusAsError* auf **false** festgelegt. 

Wenn *RestartContainerOnUnhealthyDockerHealthStatus* auf **true** festgelegt ist, wird ein Container, für den wiederholt ein nicht fehlerfreier Zustand (unhealthy) gemeldet wird, neu gestartet (ggf. auf anderen Knoten).

Wenn *TreatContainerUnhealthyStatusAsError* auf **true** festgelegt ist, werden Integritätsmeldungen als **ERROR** angezeigt, wenn der *health_status* des Containers *unhealthy* lautet.

Falls Sie die **HEALTHCHECK**-Integration für den gesamten Service Fabric-Cluster deaktivieren möchten, müssen Sie [EnableDockerHealthCheckIntegration](service-fabric-cluster-fabric-settings.md) auf **false** festlegen.

## <a name="deploy-the-application"></a>Bereitstellen der Anwendung
Die erstellte Anwendung kann mithilfe der Service Fabric-Befehlszeilenschnittstelle im lokalen Cluster bereitgestellt werden.

Stellen Sie eine Verbindung mit dem lokalen Service Fabric-Cluster her.

```bash
sfctl cluster select --endpoint http://localhost:19080
```

Verwenden Sie das in den Vorlagen bereitgestellte Installationsskript, https://github.com/Azure-Samples/service-fabric-containers/um das Anwendungspaket in den Imagespeicher des Clusters zu kopieren, den Anwendungstyp zu registrieren und eine Instanz der Anwendung zu erstellen.


```bash
./install.sh
```

Navigieren Sie in einem Browser zu Service Fabric Explorer (http:\//localhost:19080/Explorer). Falls Sie Vagrant unter Mac OS X verwenden, ersetzen Sie „localhost“ durch die private IP-Adresse des virtuellen Computers. Erweitern Sie den Knoten „Anwendungen“. Hier finden Sie nun einen Eintrag für Ihren Anwendungstyp und einen weiteren für die erste Instanz dieses Typs.

Stellen Sie eine Verbindung mit dem ausgeführten Container her. Öffnen Sie einen Webbrowser, und verweisen Sie auf die IP-Adresse, die über Port 4000 zurückgegeben wurde, z.B. „http:\//localhost:4000“. Die Überschrift „Hello World!“ wird im Browser angezeigt.

![Hello World!][hello-world]


## <a name="clean-up"></a>Bereinigung
Verwenden Sie das in der Vorlage bereitgestellte Deinstallationsskript, um die Anwendungsinstanz aus dem lokalen Entwicklungscluster zu löschen und die Registrierung des Anwendungstyps aufzuheben.

```bash
./uninstall.sh
```

Wenn Sie das Image mithilfe von Push an die Containerregistrierung übertragen haben, können Sie das lokale Image von Ihrem Entwicklungscomputer löschen:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Vollständige Beispiele für Service Fabric-Anwendungs- und Dienstmanifeste
Dies sind die vollständigen Dienst- und Anwendungsmanifeste, die in diesem Artikel verwendet werden.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="myservicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="myserviceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying containers 
      to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <!-- Pass comma delimited commands to your container: dotnet, myproc.dll, 5" -->
        <!--Commands> dotnet, myproc.dll, 5 </Commands-->
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    
    <EnvironmentVariables>
      <!--
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
      -->
    </EnvironmentVariables>
    
  </CodePackage>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myServiceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="mycontainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="myservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="myservice">
      <!-- On a local development cluster, set InstanceCount to 1. On a multi-node production 
      cluster, set InstanceCount to -1 for the container service to run on every node in 
      the cluster.
      -->
      <StatelessService ServiceTypeName="myserviceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="adding-more-services-to-an-existing-application"></a>Hinzufügen weiterer Dienste zu einer vorhandenen Anwendung

Führen Sie zum Hinzufügen eines weiteren Containerdiensts zu einer Anwendung, die bereits mit Yeoman erstellt wurde, die folgenden Schritte aus:

1. Legen Sie das Verzeichnis auf den Stamm der vorhandenen Anwendung fest. Beispiel: `cd ~/YeomanSamples/MyApplication`, wenn `MyApplication` die von Yeoman erstellte Anwendung ist.
2. Ausführen von `yo azuresfcontainer:AddService`

<a id="manually"></a>


## <a name="configure-time-interval-before-container-is-force-terminated"></a>Konfigurieren des Zeitintervalls vor Erzwingung der Containerbeendigung

Sie können für die Runtime konfigurieren, wie lange nach dem Starten der Dienstlöschung (oder der Verschiebung auf einen anderen Knoten) mit dem Entfernen des Containers gewartet werden soll. Durch die Konfiguration des Zeitintervalls wird der Befehl `docker stop <time in seconds>` an den Container gesendet.  Weitere Informationen finden Sie unter [docker stop](https://docs.docker.com/engine/reference/commandline/stop/). Die Wartezeit wird im Abschnitt `Hosting` angegeben. Im folgenden Codeausschnitt des Clustermanifests ist dargestellt, wie die Wartezeit festgelegt wird:


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "ContainerDeactivationTimeout",
                "value" : "10"
          },
          ...
        ]
}
```

Das Standardzeitintervall ist auf 10 Sekunden festgelegt. Da es sich hierbei um eine dynamische Konfiguration handelt, wird bei einem reinen Konfigurationsupgrade für den Cluster das Timeout aktualisiert. 

## <a name="configure-the-runtime-to-remove-unused-container-images"></a>Konfigurieren der Runtime für die Entfernung nicht verwendeter Containerimages

Sie können den Service Fabric-Cluster so konfigurieren, dass er nicht verwendete Containerimages aus dem Knoten entfernt. Diese Konfiguration ermöglicht die Freigabe von Speicherplatz, wenn zu viele Containerimages auf dem Knoten vorhanden sind. Aktualisieren Sie zur Aktivierung dieses Features den Abschnitt `Hosting` im Clustermanifest, wie im folgenden Codeausschnitt gezeigt: 


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "PruneContainerImages",
                "value": "True"
          },
          {
                "name": "ContainerImagesToSkip",
                "value": "mcr.microsoft.com/windows/servercore|mcr.microsoft.com/windows/nanoserver|mcr.microsoft.com/dotnet/framework/aspnet|..."
          }
          ...
          }
        ]
} 
```

Images, die nicht gelöscht werden sollen, können Sie mithilfe des Parameters `ContainerImagesToSkip` angeben. 

## <a name="configure-container-image-download-time"></a>Konfigurieren der Downloadzeit für Containerimages

Von der Service Fabric-Runtime werden 20 Minuten für das Herunterladen und Extrahieren von Containerimages zugeteilt. Dies ist für die meisten Containerimages ausreichend. Für große Images oder bei einer langsamen Netzwerkverbindung kann es erforderlich sein, die Wartezeit bis zu dem Zeitpunkt, zu dem das Herunterladen und Extrahieren des Images abgebrochen wird, zu erhöhen. Dieses Timeout kann mit dem Attribut **ContainerImageDownloadTimeout** im Abschnitt **Hosting** des Clustermanifests festgelegt werden, wie im folgenden Codeausschnitt zu sehen:

```json
{
        "name": "Hosting",
        "parameters": [
          {
              "name": "ContainerImageDownloadTimeout",
              "value": "1200"
          }
        ]
}
```


## <a name="set-container-retention-policy"></a>Festlegen der Aufbewahrungsrichtlinie für Container

Zur Diagnose von Startfehlern unterstützt Service Fabric (ab Version 6.1) die Aufbewahrung von Containern, die beendet wurden oder nicht gestartet werden konnten. Diese Richtlinie kann in der Datei **ApplicationManifest.xml** festgelegt werden, wie im folgenden Codeausschnitt gezeigt:

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

Mit der Einstellung **ContainersRetentionCount** wird die Anzahl von Containern angegeben, die bei Fehlern beibehalten werden sollen. Wenn ein negativer Wert angegeben wird, werden alle fehlerhaften Container beibehalten. Wenn das Attribut **ContainersRetentionCount** nicht angegeben wird, werden keine Container beibehalten. Das Attribut **ContainersRetentionCount** unterstützt auch Anwendungsparameter, sodass Benutzer unterschiedliche Werte für Test- und Produktionscluster angeben können. Nutzen Sie bei Verwendung dieses Features Platzierungseinschränkungen, um den Containerdienst einem bestimmten Knoten zuzuordnen und zu verhindern, dass er auf andere Knoten verschoben wird. Alle Container, die mit diesem Feature beibehalten werden, müssen manuell entfernt werden.

## <a name="start-the-docker-daemon-with-custom-arguments"></a>Starten des Docker-Daemons mit benutzerdefinierten Argumenten

Ab Version 6.2 der Service Fabric-Runtime können Sie den Docker-Daemon mit benutzerdefinierten Argumenten starten. Wenn Sie benutzerdefinierte Argumente angegeben, übergibt Service Fabric ausschließlich das Argument `--pidfile` an die Docker-Engine. `--pidfile` darf daher nicht als Argument übergeben werden. Außerdem muss das Argument weiterhin sicherstellen, dass der Docker-Daemon unter Windows an der standardmäßigen Named Pipe (bzw. unter Linux am Unix-Domänensocket) lauscht, damit Service Fabric mit dem Daemon kommunizieren kann. Die benutzerdefinierten Argumente werden im Clustermanifest im Abschnitt **Hosting** unter **ContainerServiceArguments** angegeben. Ein Beispiel sehen Sie im folgenden Codeausschnitt: 
 

```json
{ 
        "name": "Hosting", 
        "parameters": [ 
          { 
            "name": "ContainerServiceArguments", 
            "value": "-H localhost:1234 -H unix:///var/run/docker.sock" 
          } 
        ] 
} 

```

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum Ausführen von [Containern in Service Fabric](service-fabric-containers-overview.md)
* Lesen Sie sich das Tutorial [Bereitstellen einer .NET-App in einem Container in Azure Service Fabric](service-fabric-host-app-in-a-container.md) durch.
* Weitere Informationen zum [Anwendungslebenszyklus](service-fabric-application-lifecycle.md) von Service Fabric
* [Codebeispiele zu Service Fabric-Containern](https://github.com/Azure-Samples/service-fabric-containers) in GitHub

[hello-world]: ./media/service-fabric-get-started-containers-linux/HelloWorld.png
[sf-yeoman]: ./media/service-fabric-get-started-containers-linux/YoSF.png

[1]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[2]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png
