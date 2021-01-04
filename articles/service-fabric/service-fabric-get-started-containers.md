---
title: Erstellen einer Azure Service Fabric-Containeranwendung
description: Erstellen Sie Ihre erste Windows-Containeranwendung unter Azure Service Fabric. Erstellen Sie ein Docker-Image mit einer Python-Anwendung, pushen Sie es an eine Containerregistrierung, erstellen Sie den Container, und stellen Sie ihn dann in Azure Service Fabric bereit.
ms.topic: conceptual
ms.date: 01/25/2019
ms.custom: devx-track-python
ms.openlocfilehash: 197423670ffe05f15fdc5bfd351efdfba33b53cd
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533773"
---
# <a name="create-your-first-service-fabric-container-application-on-windows"></a>Erstellen Ihrer ersten Service Fabric-Containeranwendung unter Windows

> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

Zum Ausführen einer vorhandenen Anwendung eines Windows-Containers in einem Service Fabric-Cluster sind keine Änderungen an Ihrer Anwendung erforderlich. In diesem Artikel werden die einzelnen Schritte vorgestellt, mit denen Sie ein Docker-Image mit einer Python [Flask](http://flask.pocoo.org/)-Webanwendung erstellen und in einem Azure Service Fabric-Cluster bereitstellen. Außerdem stellen Sie Ihre Containeranwendung per [Azure Container Registry](../container-registry/index.yml) bereit. In diesem Artikel werden grundlegende Kenntnisse von Docker vorausgesetzt. Weitere Informationen zu Docker finden Sie in der [Docker-Übersicht](https://docs.docker.com/engine/understanding-docker/).

> [!NOTE]
> Dieser Artikel bezieht sich auf eine Windows-Entwicklungsumgebung.  Die Service Fabric-Clusterruntime und die Docker-Runtime müssen unter dem gleichen Betriebssystem ausgeführt werden.  Sie können Windows-Container nicht in einem Linux-Cluster ausführen.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Ein Entwicklungscomputer, auf dem Folgendes ausgeführt wird:
  * Visual Studio 2015 oder Visual Studio 2019.
  * [Service Fabric-SDK und -Tools](service-fabric-get-started.md)
  *  Docker für Windows [Laden Sie „Docker CE for Windows (stable)“ herunter](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Klicken Sie nach dem Installieren und Starten von Docker mit der rechten Maustaste auf das Taskleistensymbol, und wählen Sie **Switch to Windows containers** (Zu Windows-Containern wechseln). Dieser Schritt ist für die Ausführung Windows-basierter Docker-Images erforderlich.

* Ein Windows-Cluster mit mindestens drei Knoten, auf denen Windows Server ausgeführt wird, mit Containern 

  Für diesen Artikel muss die Version (Build) von Windows Server mit Containern, die auf den Clusterknoten ausgeführt wird, mit der auf dem Entwicklungscomputer übereinstimmen. Dies ist erforderlich, da Sie das Docker-Image auf dem Entwicklungscomputer erstellen und zwischen den Versionen des Containerbetriebssystems und des Hostbetriebssystems für die Bereitstellung Kompatibilitätseinschränkungen bestehen. Weitere Informationen finden Sie unter [Kompatibilität zwischen Containerbetriebssystem und Hostbetriebssystem bei Windows Server](#windows-server-container-os-and-host-os-compatibility). 
  
    Zum Ermitteln der Version von Windows Server mit Containern, die Sie für Ihren Cluster benötigen, führen Sie den Befehl `ver` an einer Windows-Eingabeaufforderung auf dem Entwicklungscomputer aus. Lesen Sie [Kompatibilität des Windows Server-Containerbetriebssystems und des Hostbetriebssystems](#windows-server-container-os-and-host-os-compatibility), bevor Sie [einen Cluster erstellen](service-fabric-cluster-creation-via-portal.md).

* Eine Registrierung in Azure Container Registry – erstellen Sie in Ihrem Azure-Abonnement eine [Containerregistrierung](../container-registry/container-registry-get-started-portal.md).

> [!NOTE]
> Das Bereitstellen von Containern für einen unter Windows 10 ausgeführten Service Fabric-Cluster wird nicht unterstützt.  In [diesem Artikel](service-fabric-how-to-debug-windows-containers.md) finden Sie Informationen dazu, wie Windows 10 für die Ausführung von Windows-Container konfiguriert werden kann.

> [!NOTE]
> Service Fabric Versionen 6.2 und höher unterstützen die Bereitstellung von Containern für Cluster, die auf Windows Server Version 1709 ausgeführt werden.

## <a name="define-the-docker-container"></a>Definieren des Docker-Containers

Erstellen Sie ein Image auf Grundlage des [Python-Image](https://hub.docker.com/_/python/) in Docker Hub.

Geben Sie Ihren Docker-Container in einer Dockerfile-Datei an. Die Dockerfile-Datei enthält Anweisungen für die Einrichtung der Umgebung in Ihrem Container, für das Laden der auszuführenden Anwendung und für die Portzuordnung. Die Dockerfile-Datei ist die Eingabe für den Befehl `docker build`, der das Image erstellt.

Erstellen Sie ein leeres Verzeichnis, und erstellen Sie die Datei *Dockerfile* (ohne Dateierweiterung). Fügen Sie der *Dockerfile* Folgendes hinzu, und speichern Sie Ihre Änderungen:

```
# Use an official Python runtime as a base image
FROM python:2.7-windowsservercore

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Weitere Informationen finden Sie in der [Dockerfile-Referenz](https://docs.docker.com/engine/reference/builder/).

## <a name="create-a-basic-web-application"></a>Erstellen einer einfachen Webanwendung
Erstellen Sie eine Flask-Webanwendung, die an Port 80 lauscht und `Hello World!` zurückgibt. Erstellen Sie in demselben Verzeichnis die Datei *requirements.txt*. Fügen Sie Folgendes hinzu, und speichern Sie die Änderungen:
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

<a id="Build-Containers"></a>

## <a name="login-to-docker-and-build-the-image"></a>Anmelden bei Docker und Erstellen des Images

Nun erstellen Sie das Image, mit dem Ihre Webanwendung ausgeführt wird. Beim Pullen von öffentlichen Images aus Docker (wie `python:2.7-windowsservercore` in unserer Dockerfile-Datei) besteht eine bewährte Methode darin, sich mit Ihrem Docker Hub-Konto zu authentifizieren, anstatt einen anonymen Pull Request zu erstellen.

> [!NOTE]
> Beim Ausführen von häufigen anonymen Pull Requests wird möglicherweise ein Docker-Fehler wie `ERROR: toomanyrequests: Too Many Requests.` oder `You have reached your pull rate limit.` angezeigt. Authentifizieren Sie sich bei Docker Hub, um diese Fehler zu verhindern. Weitere Informationen finden Sie unter [Verwalten öffentlicher Inhalte mit Azure Container Registry](../container-registry/buffer-gate-public-content.md).

Öffnen Sie ein PowerShell-Fenster, und navigieren Sie zu dem Verzeichnis, das die Dockerfile-Datei enthält. Führen Sie anschließend die folgenden Befehle aus:

```
docker login
docker build -t helloworldapp .
```

Dieser Befehl erstellt das neue Image gemäß den Anweisungen aus der Dockerfile-Datei. Das Image erhält den Namen `helloworldapp`. Hierzu wird der Parameter „-t“ (für Tagging) verwendet. Zur Erstellung eines Containerimages wird zunächst das Basisimage von Docker Hub heruntergeladen, und die Anwendung wird hinzugefügt. 

Wenn der Buildbefehl abgeschlossen ist, rufen Sie den Befehl `docker images` ab, um Informationen zum neuen Image anzuzeigen:

```
$ docker images

REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              8ce25f5d6a79        2 minutes ago       10.4 GB
```

## <a name="run-the-application-locally"></a>Lokales Ausführen der Anwendung
Überprüfen Sie Ihr Image lokal, bevor Sie es mithilfe von Push an die Containerregistrierung übertragen. 

Führen Sie die Anwendung aus.

```
docker run -d --name my-web-site helloworldapp
```

*name* vergibt einen Namen für den ausgeführten Container (anstelle der Container-ID).

Ermitteln Sie nach dem Start des Containers seine IP-Adresse, damit Sie über einen Browser eine Verbindung mit Ihrem ausgeführten Container herstellen können:
```
docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
```

Wenn dieser Befehl kein Ergebnis zurückgibt, führen Sie den folgenden Befehl aus, und sehen Sie sich das Element **NetworkSettings**->**Networks** für die IP-Adresse an:
```
docker inspect my-web-site
```

Stellen Sie eine Verbindung mit dem ausgeführten Container her. Öffnen Sie einen Webbrowser, und verweisen Sie auf die zurückgegebene IP-Adresse, etwa „http:\//172.31.194.61“. Die Überschrift „Hello World!“ wird im Browser angezeigt.

Führen Sie Folgendes aus, um den Container zu beenden:

```
docker stop my-web-site
```

Löschen Sie den Container von Ihrem Entwicklungscomputer:

```
docker rm my-web-site
```

<a id="Push-Containers"></a>
## <a name="push-the-image-to-the-container-registry"></a>Übertragen des Images an die Containerregistrierung mithilfe von Push

Wenn Sie überprüft haben, ob der Container auf dem Entwicklungscomputer ausgeführt wird, übertragen Sie das Image mithilfe von Push an Ihre Registrierung in Azure Container Registry.

Führen Sie ``docker login`` aus, um sich mit Ihren [Registrierungsanmeldeinformationen](../container-registry/container-registry-authentication.md) bei der Containerregistrierung anzumelden.

Im folgenden Beispiel werden die ID und das Kennwort eines Azure Active Directory-[Dienstprinzipals](../active-directory/develop/app-objects-and-service-principals.md) übergeben. Angenommen, Sie haben Ihrer Registrierung für ein Automatisierungsszenario einen Dienstprinzipal zugewiesen. Alternativ können Sie sich mit Ihrem für die Registrierung verwendeten Benutzernamen und Kennwort anmelden.

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Mit dem folgenden Befehl wird ein Tag oder Alias des Images mit einem vollqualifizierten Pfad zur Registrierung erstellt. In diesem Beispiel wird das Image im ```samples```-Namespace platziert, um den Stamm der Registrierung nicht zu überladen.

```
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Übertragen des Image mithilfe von Push an Ihre Containerregistrierung:

```
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="create-the-containerized-service-in-visual-studio"></a>Erstellen des im Container ausgeführten Diensts in Visual Studio
Das Service Fabric-SDK und die Tools stellen eine Dienstvorlage bereit, um Sie beim Erstellen einer Containeranwendung zu unterstützen.

1. Starten Sie Visual Studio. Klicken Sie auf **Datei** > **Neu** > **Projekt**.
2. Wählen Sie **Service Fabric-Anwendung**, benennen Sie sie „MyFirstContainer“, und klicken Sie auf **OK**.
3. Wählen Sie in der Liste **Dienstvorlagen** die Option **Container** aus.
4. Geben Sie unter **Imagename** „myregistry.azurecr.io/samples/helloworldapp“ ein, das Image, das Sie mithilfe von Push an Ihr Containerrepository übertragen haben.
5. Geben Sie dem Dienst einen Namen, und klicken Sie auf **OK**.

## <a name="configure-communication"></a>Konfigurieren der Kommunikation
Der im Container ausgeführte Dienst erfordert einen Endpunkt für die Kommunikation. Fügen Sie der Datei „ServiceManifest.xml“ ein `Endpoint`-Element mit dem Protokoll, dem Port und dem Typ hinzu. In diesem Beispiel wird der feste Port 8081 verwendet. Wenn kein Port angegeben ist, wird ein zufälliger Port im Portbereich für die Anwendung ausgewählt. 

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
  </Endpoints>
</Resources>
```
> [!NOTE]
> Zusätzliche Endpunkte für einen Dienst können hinzugefügt werden, indem zusätzliche EndPoint-Elemente mit entsprechenden Eigenschaftswerten deklariert werden. Jeder Port kann nur einen Protokollwert deklarieren.

Durch die Definition eines Endpunkts veröffentlicht Service Fabric den Endpunkt an den Naming Service. Dieser Container kann von anderen im Cluster ausgeführten Diensten aufgelöst werden. Mithilfe des [Reverseproxys](service-fabric-reverseproxy.md) können Sie auch Container-zu-Container-Kommunikation durchführen. Die Kommunikation erfolgt, indem der HTTP-Lauschport für den Reverseproxy und die Namen der Dienste, mit denen Sie kommunizieren möchten, als Umgebungsvariablen angegeben werden.

Der Dienst lauscht an einem bestimmten Port (in diesem Beispiel: 8081). Bei der Bereitstellung eines Clusters in Azure durch die Anwendung werden sowohl der Cluster als auch die Anwendung hinter einem Azure-Lastenausgleichsmodul ausgeführt. Der Anwendungsport muss im Azure-Lastenausgleichsmodul geöffnet sein, damit eingehender Datenverkehr an den Dienst gesendet werden kann.  Sie können diesen Port im Azure-Lastenausgleichsmodul mithilfe eines [PowerShell-Skripts](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) oder im [Azure-Portal](https://portal.azure.com) öffnen.

## <a name="configure-and-set-environment-variables"></a>Konfigurieren und Festlegen von Umgebungsvariablen
Zu jedem Codepaket im Dienstmanifest können Umgebungsvariablen angegeben werden. Diese Funktion ist für alle Dienste verfügbar, unabhängig davon, ob diese als Container, Prozesse oder ausführbare Gastdateien bereitgestellt werden. Die können die Werte von Umgebungsvariablen im Anwendungsmanifest überschreiben oder während der Bereitstellung als Anwendungsparameter angeben.

Der folgende XML-Codeausschnitt eines Dienstmanifests enthält ein Beispiel für die Angabe von Umgebungsvariablen für ein Codepaket:
```xml
<CodePackage Name="Code" Version="1.0.0">
  ...
  <EnvironmentVariables>
    <EnvironmentVariable Name="HttpGatewayPort" Value=""/>    
  </EnvironmentVariables>
</CodePackage>
```

Diese Umgebungsvariablen können im Anwendungsmanifest überschrieben werden:

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
  <EnvironmentOverrides CodePackageRef="FrontendService.Code">
    <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
  </EnvironmentOverrides>
  ...
</ServiceManifestImport>
```

## <a name="configure-container-port-to-host-port-mapping-and-container-to-container-discovery"></a>Konfigurieren der Zuordnung vom Containerport zum Hostport und der Container-zu-Container-Ermittlung
Konfigurieren Sie einen Hostport für die Kommunikation mit dem Container. Über die Portbindung wird der Port, über den der Dienst im Container lauscht, einem Port auf dem Host zugeordnet. Fügen Sie dem `ContainerHostPolicies`-Element der Datei „ApplicationManifest.xml“ ein `PortBinding`-Element hinzu. In diesem Artikel ist `ContainerPort` 80 (der Container macht Port 80 verfügbar, wie in der Dockerfile-Datei angegeben), und `EndpointRef` ist „Guest1TypeEndpoint“ (der zuvor im Dienstmanifest angegebene Endpunkt). Über Port 8081 an den Dienst eingehende Anforderungen werden Port 80 im Container zugeordnet.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```
> [!NOTE]
> Zusätzliche PortBindings für einen Dienst können hinzugefügt werden, indem zusätzliche PortBinding-Elemente mit entsprechenden Eigenschaftswerten deklariert werden.

## <a name="configure-container-repository-authentication"></a>Konfigurieren der Authentifizierung des Containerrepositorys

Informationen dazu, wie Sie verschiedene Authentifizierungstypen für das Herunterladen von Containerimages konfigurieren, finden Sie unter [Authentifizierung für Containerrepositorys](configure-container-repository-credentials.md).

## <a name="configure-isolation-mode"></a>Isolationsmodus konfigurieren
Windows unterstützt zwei Isolationsmodi für Container: Prozesse und Hyper-V. Mit dem Prozessisolationsmodus verwenden alle auf demselben Host ausgeführten Container denselben Kernel wie der Host. Mit dem Hyper-V-Isolationsmodus werden die Kernels der einzelnen Hyper-V-Container und des Containerhosts voneinander getrennt. Der Isolationsmodus wird im `ContainerHostPolicies`-Element der Anwendungsmanifestdatei angegeben. Als Isolationsmodi können `process`, `hyperv` und `default` angegeben werden. In der Voreinstellung wird auf Windows Server-Hosts der Prozessisolationsmodus verwendet. Auf Windows 10-Hosts wird nur der Hyper-V-Isolationsmodus unterstützt, daher wird der Container im Hyper-V-Isolationsmodus unabhängig von der Einstellung für den Isolationsmodus ausgeführt. Im folgenden Codeausschnitt wird gezeigt, wie der Isolationsmodus in der Anwendungsmanifestdatei angegeben wird.

```xml
<ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
```
   > [!NOTE]
   > Der hyperv-Isolationsmodus ist unter Ev3- und Dv3-Azure-SKUs verfügbar, die die geschachtelte Virtualisierung unterstützen. 
   >
   >

## <a name="configure-resource-governance"></a>Konfigurieren der Ressourcenkontrolle
Die [Ressourcenkontrolle](service-fabric-resource-governance.md) beschränkt die Ressourcen, die vom Container auf dem Host verwendet werden können. Mit dem `ResourceGovernancePolicy`-Element, das im Anwendungsmanifest angegeben ist, werden Ressourceneinschränkungen für ein Dienstcodepaket deklariert. Ressourcenlimits können für die folgenden Ressourcen festgelegt werden: Arbeitsspeicher, MemorySwap, CpuShares (relative CPU-Gewichtung), MemoryReservationInMB, BlkioWeight (relative BlockIO-Gewichtung). In diesem Beispiel erhält das Dienstpaket „Guest1Pkg“ einen Kern auf den Clusterknoten, auf denen es platziert wurde. Die Einschränkungen des Arbeitsspeichers sind absolut, daher ist das Codepaket auf 1024 MB Arbeitsspeicher (sowie eine weiche Reservierungsgarantie desselben) beschränkt. Codepakete (Container oder Prozesse) können nicht mehr Arbeitsspeicher zuzuweisen, als dieser Grenzwert zulässt, ein entsprechender Versuch führt daher zu einer Ausnahme „Nicht genügend Arbeitsspeicher“. Damit die Erzwingung des Ressourcenlimits funktioniert, sollten für alle Codepakete innerhalb eines Dienstpakets Arbeitsspeicherlimits festgelegt sein.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
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

![Screenshot mit Details des bereitgestellten Dienstpakets „NodeServicePackage“][3]

![HealthCheckUnhealthyApp][4]

![HealthCheckUnhealthyDsp][5]

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

## <a name="deploy-the-container-application"></a>Bereitstellen der Containeranwendung
Speichern Sie alle Änderungen, und erstellen Sie die Anwendung. Klicken Sie zum Veröffentlichen Ihrer Anwendung im Projektmappen-Explorer mit der rechten Maustaste auf **MyFirstContainer**, und wählen Sie **Veröffentlichen**.

Geben Sie in **Verbindungsendpunkt** den Verwaltungsendpunkt für den Cluster ein, Beispiel: `containercluster.westus2.cloudapp.azure.com:19000`. Den Clientverbindungsendpunkt finden Sie im [Azure-Portal](https://portal.azure.com) auf der Registerkarte „Übersicht“ für Ihren Cluster.

Klicken Sie auf **Veröffentlichen**.

[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) ist ein webbasiertes Tool zum Untersuchen und Verwalten von Anwendungen und Knoten in einem Service Fabric-Cluster. Navigieren Sie in einem Browser zu `http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/`, und führen Sie die Anwendungsbereitstellung durch. Die Anwendung wird bereitgestellt, befindet sich bis zum Herunterladen des Images auf die Clusterknoten aber in einem Fehlerzustand (je nach Imagegröße kann dies einige Zeit in Anspruch nehmen): ![Fehler][1]

Die Anwendung ist bereit, wenn Sie sich im Zustand ```Ready``` befindet: ![Bereit][2].

Öffnen Sie einen Browser, und navigieren Sie zu `http://containercluster.westus2.cloudapp.azure.com:8081`. Die Überschrift „Hello World!“ wird im Browser angezeigt.

## <a name="clean-up"></a>Bereinigung

Während der Ausführung des Clusters fallen weiterhin Gebühren an. [Löschen](./service-fabric-tutorial-delete-cluster.md) Sie Ihren Cluster daher ggf.

Wenn Sie das Image mithilfe von Push an die Containerregistrierung übertragen haben, können Sie das lokale Image von Ihrem Entwicklungscomputer löschen:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="windows-server-container-os-and-host-os-compatibility"></a>Kompatibilität zwischen Containerbetriebssystem und Hostbetriebssystem bei Windows Server

Windows Server-Container sind nicht mit allen Versionen eines Hostbetriebssystems kompatibel. Beispiel:
 
- Windows Server-Container, die mit Windows Server Version 1709 erstellt wurden, funktionieren nicht auf Hosts unter Windows Server 2016. 
- Windows Server-Container, die mit Windows Server 2016 erstellt wurden, funktionieren im Hyper-V-Isolationsmodus nur auf Hosts unter Windows Server Version 1709. 
- Mit Windows Server-Containern, die mit Windows Server 2016 erstellt wurden, ist es möglicherweise erforderlich, sicherzustellen, dass die Revision des Containerbetriebssystems und des Hostbetriebssystems sind identisch, sofern die Ausführung im Isolationsmodus auf einem Host unter Windows Server 2016 erfolgt.
 
Weitere Informationen finden Sie unter [Versionskompatibilität von Windows-Containern](/virtualization/windowscontainers/deploy-containers/version-compatibility).

Beachten Sie die Kompatibilität von Hostbetriebssystem und Containerbetriebssystem beim Erstellen und Bereitstellen von Containern in Ihrem Service Fabric-Cluster. Beispiel:

- Stellen Sie sicher, dass Sie Container mit einem Betriebssystem bereitstellen, das kompatibel mit dem Betriebssystem auf den Clusterknoten ist.
- Stellen Sie sicher, dass der für Ihre Container-App angegebene Isolationsmodus mit dem für das Containerbetriebssystem auf dem Knoten, auf dem sie bereitgestellt wird, übereinstimmt.
- Berücksichtigen Sie auch, wie sich Betriebssystemupgrades auf den Clusterknoten oder in den Containern auf deren Kompatibilität auswirken. 

Die folgenden Verfahren werden empfohlen, um sicherzustellen, dass die Container ordnungsgemäß im Service Fabric-Cluster bereitgestellt werden:

- Wenden Sie das explizite Imagetagging auf Ihre Docker-Images an, um die Version des Windows Server-Betriebssystems, auf dem ein Container basiert, anzugeben. 
- Verwenden Sie das [Betriebssystemtagging](#specify-os-build-specific-container-images) in der Anwendungsmanifestdatei, um sicherzustellen, dass Ihre Anwendung mit unterschiedlichen Versionen und Upgrades von Windows Server kompatibel ist.

> [!NOTE]
> Mit Service Fabric ab Version 6.2 können Sie Container basierend auf Windows Server 2016 lokal auf einem Windows 10-Host bereitstellen. Unter Windows 10 werden Container im Hyper-V-Isolationsmodus ausgeführt, und zwar unabhängig vom Isolationsmodus im Anwendungsmanifest. Weitere Informationen finden Sie unter [Konfigurieren des Isolationsmodus](#configure-isolation-mode).   
>
 
## <a name="specify-os-build-specific-container-images"></a>Angeben spezifischer Containerimages für den Build des Betriebssystems 

Windows Server-Container sind möglicherweise nicht mit allen Versionen des Betriebssystems kompatibel. Beispielsweise funktionieren Windows Server-Container, die mit Windows Server 2016 erstellt wurden, im Prozessisolationsmodus nicht für Windows Server Version 1709. Daher tritt für Containerdienste, die mit älteren Versionen des Betriebssystems erstellt wurden, ggf. ein Fehler auf, wenn die Clusterknoten auf die aktuelle Version aktualisiert werden. Um dies mit Version 6.1 der Runtime (und höher) zu umgehen, unterstützt Service Fabric das Angeben mehrerer Betriebssystemimages pro Container und das Tagging mit den Buildversionen des Betriebssystems im Anwendungsmanifest. Sie rufen die Buildversion des Betriebssystems ab, indem Sie `winver` an einer Windows-Eingabeaufforderung ausführen. Aktualisieren Sie zunächst die Anwendungsmanifeste, und geben Sie Imageaußerkraftsetzungen pro Betriebssystemversion an, bevor Sie das Betriebssystem auf den Knoten aktualisieren. Im folgenden Codeausschnitt wird veranschaulicht, wie Sie im Anwendungsmanifest (**ApplicationManifest.xml**) mehrere Containerimages angeben:


```xml
      <ContainerHostPolicies> 
         <ImageOverrides> 
           <Image Name="myregistry.azurecr.io/samples/helloworldappDefault" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1701" Os="14393" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1709" Os="16299" /> 
         </ImageOverrides> 
      </ContainerHostPolicies> 
```
Die Buildversion für Windows Server 2016 lautet 14393, und die Buildversion für Windows Server-Version 1709 lautet 16299. Im Dienstmanifest wird weiterhin nur ein Image pro Containerdienst angegeben, wie hier zu sehen ist:

```xml
<ContainerHost>
    <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName> 
</ContainerHost>
```

   > [!NOTE]
   > Das Feature für die Kennzeichnung nach der Buildversion des Betriebssystems ist nur für Service Fabric unter Windows verfügbar.
   >

Falls das zugrunde liegende Betriebssystem auf der VM Build 16299 (Version 1709) ist, wird von Service Fabric das Containerimage gewählt, das dieser Windows Server-Version entspricht. Wenn im Anwendungsmanifest neben gekennzeichneten Containerimages auch ein nicht gekennzeichnetes Containerimage angegeben ist, behandelt Service Fabric dieses nicht gekennzeichnete Image als eine Komponente, die versionsübergreifend funktioniert. Markieren Sie die Containerimages explizit, um Probleme bei Upgrades zu vermeiden.

Das nicht gekennzeichnete Containerimage setzt das Image außer Kraft, das im ServiceManifest-Element angegeben wurde. Mit dem Image „myregistry.azurecr.io/samples/helloworldappDefault“ wird also der Imagename (ImageName) „myregistry.azurecr.io/samples/helloworldapp“ im ServiceManifest-Element außer Kraft gesetzt.

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Vollständige Beispiele für Service Fabric-Anwendungs- und Dienstmanifeste
Dies sind die vollständigen Dienst- und Anwendungsmanifeste, die in diesem Artikel verwendet werden.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <!-- Pass comma delimited commands to your container: dotnet, myproc.dll, 5" -->
        <!--Commands> dotnet, myproc.dll, 5 </Commands-->
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->    
    <EnvironmentVariables>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
    </EnvironmentVariables>

  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to
           listen. Please note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentOverrides CodePackageRef="FrontendService.Code">
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentOverrides>
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="MIIB6QYJKoZIhvcNAQcDoIIB2jCCAdYCAQAxggFRMIIBTQIBADA1MCExHzAdBgNVBAMMFnJ5YW53aWRhdGFlbmNpcGhlcm1lbnQCEFfyjOX/17S6RIoSjA6UZ1QwDQYJKoZIhvcNAQEHMAAEg
gEAS7oqxvoz8i6+8zULhDzFpBpOTLU+c2mhBdqXpkLwVfcmWUNA82rEWG57Vl1jZXe7J9BkW9ly4xhU8BbARkZHLEuKqg0saTrTHsMBQ6KMQDotSdU8m8Y2BR5Y100wRjvVx3y5+iNYuy/JmM
gSrNyyMQ/45HfMuVb5B4rwnuP8PAkXNT9VLbPeqAfxsMkYg+vGCDEtd8m+bX/7Xgp/kfwxymOuUCrq/YmSwe9QTG3pBri7Hq1K3zEpX4FH/7W2Zb4o3fBAQ+FuxH4nFjFNoYG29inL0bKEcTX
yNZNKrvhdM3n1Uk/8W2Hr62FQ33HgeFR1yxQjLsUu800PrYcR5tLfyTB8BgkqhkiG9w0BBwEwHQYJYIZIAWUDBAEqBBBybgM5NUV8BeetUbMR8mJhgFBrVSUsnp9B8RyebmtgU36dZiSObDsI
NtTvlzhk11LIlae/5kjPv95r3lw6DHmV4kXLwiCNlcWPYIWBGIuspwyG+28EWSrHmN7Dt2WqEWqeNQ==" PasswordEncrypted="true"/>
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this
         application type is created. You can also create one or more instances of service type using the
         ServiceFabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="configure-time-interval-before-container-is-force-terminated"></a>Konfigurieren des Zeitintervalls vor Erzwingung der Containerbeendigung

Sie können für die Runtime konfigurieren, wie lange nach dem Starten der Dienstlöschung (oder der Verschiebung auf einen anderen Knoten) mit dem Entfernen des Containers gewartet werden soll. Durch die Konfiguration des Zeitintervalls wird der Befehl `docker stop <time in seconds>` an den Container gesendet.  Weitere Informationen finden Sie unter [docker stop](https://docs.docker.com/engine/reference/commandline/stop/). Die Wartezeit wird im Abschnitt `Hosting` angegeben. Der `Hosting`-Abschnitt kann bei der Clustererstellung oder später in einem Konfigurationsupgrade hinzugefügt werden. Im folgenden Codeausschnitt des Clustermanifests ist dargestellt, wie die Wartezeit festgelegt wird:

```json
"fabricSettings": [
    ...,
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
]
```
Das Standardzeitintervall ist auf 10 Sekunden festgelegt. Da es sich hierbei um eine dynamische Konfiguration handelt, wird bei einem reinen Konfigurationsupgrade für den Cluster das Timeout aktualisiert. 


## <a name="configure-the-runtime-to-remove-unused-container-images"></a>Konfigurieren der Runtime für die Entfernung nicht verwendeter Containerimages

Sie können den Service Fabric-Cluster so konfigurieren, dass er nicht verwendete Containerimages aus dem Knoten entfernt. Diese Konfiguration ermöglicht die Freigabe von Speicherplatz, wenn zu viele Containerimages auf dem Knoten vorhanden sind. Aktualisieren Sie zur Aktivierung dieses Features den Abschnitt [Hosting](service-fabric-cluster-fabric-settings.md#hosting) im Clustermanifest, wie im folgenden Codeausschnitt gezeigt: 


```json
"fabricSettings": [
    ...,
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
]
```

Images, die nicht gelöscht werden sollen, können Sie mithilfe des Parameters `ContainerImagesToSkip` angeben.  


## <a name="configure-container-image-download-time"></a>Konfigurieren der Downloadzeit für Containerimages

Von der Service Fabric-Runtime werden 20 Minuten für das Herunterladen und Extrahieren von Containerimages zugeteilt. Dies ist für die meisten Containerimages ausreichend. Für große Images oder bei einer langsamen Netzwerkverbindung kann es erforderlich sein, die Wartezeit bis zu dem Zeitpunkt, zu dem das Herunterladen und Extrahieren des Images abgebrochen wird, zu erhöhen. Dieses Timeout kann mit dem Attribut **ContainerImageDownloadTimeout** im Abschnitt **Hosting** des Clustermanifests festgelegt werden, wie im folgenden Codeausschnitt zu sehen:

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
              "name": "ContainerImageDownloadTimeout",
              "value": "1200"
          }
        ]
    }
]
```


## <a name="set-container-retention-policy"></a>Festlegen der Aufbewahrungsrichtlinie für Container

Als Hilfe bei der Diagnose von Startfehlern unterstützt Service Fabric (Version 6.1 oder höher) das Aufbewahren von Containern, die beendet wurden oder nicht gestartet werden konnten. Diese Richtlinie kann in der Datei **ApplicationManifest.xml** festgelegt werden, wie im folgenden Codeausschnitt gezeigt:

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

Mit der Einstellung **ContainersRetentionCount** wird die Anzahl von Containern angegeben, die bei Fehlern beibehalten werden sollen. Wenn ein negativer Wert angegeben wird, werden alle fehlerhaften Container beibehalten. Wenn das Attribut **ContainersRetentionCount** nicht angegeben wird, werden keine Container beibehalten. Das Attribut **ContainersRetentionCount** unterstützt auch Anwendungsparameter, sodass Benutzer unterschiedliche Werte für Test- und Produktionscluster angeben können. Nutzen Sie bei Verwendung dieses Features Platzierungseinschränkungen, um den Containerdienst einem bestimmten Knoten zuzuordnen und zu verhindern, dass er auf andere Knoten verschoben wird. Alle Container, die mit diesem Feature beibehalten werden, müssen manuell entfernt werden.

## <a name="start-the-docker-daemon-with-custom-arguments"></a>Starten des Docker-Daemons mit benutzerdefinierten Argumenten

Ab Version 6.2 der Service Fabric-Runtime können Sie den Docker-Daemon mit benutzerdefinierten Argumenten starten. Wenn Sie benutzerdefinierte Argumente angegeben, übergibt Service Fabric ausschließlich das Argument `--pidfile` an die Docker-Engine. `--pidfile` darf daher nicht als Argument übergeben werden. Außerdem muss das Argument weiterhin sicherstellen, dass der Docker-Daemon unter Windows an der standardmäßigen Named Pipe (bzw. unter Linux am Unix-Domänensocket) lauscht, damit Service Fabric mit dem Daemon kommunizieren kann. Die benutzerdefinierten Argumente werden im Clustermanifest im Abschnitt **Hosting** unter **ContainerServiceArguments** übergeben, wie im folgenden Codeausschnitt zu sehen: 
 

```json
"fabricSettings": [
    ...,
    { 
        "name": "Hosting", 
        "parameters": [ 
          { 
            "name": "ContainerServiceArguments", 
            "value": "-H localhost:1234 -H unix:///var/run/docker.sock" 
          } 
        ] 
    } 
]
```

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum Ausführen von [Containern in Service Fabric](service-fabric-containers-overview.md)
* Lesen Sie sich das Tutorial [Bereitstellen einer .NET-App in einem Container in Azure Service Fabric](service-fabric-host-app-in-a-container.md) durch.
* Weitere Informationen zum [Anwendungslebenszyklus](service-fabric-application-lifecycle.md) von Service Fabric
* [Codebeispiele zu Service Fabric-Containern](https://github.com/Azure-Samples/service-fabric-containers) in GitHub

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[4]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[5]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png
