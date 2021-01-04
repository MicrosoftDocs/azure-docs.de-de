---
title: Erstellen eines Containers für Apache Tomcat unter Linux
description: In diesem Artikel erfahren Sie, wie Sie Linux-Container erstellen können, um eine auf einem Apache Tomcat-Server in Azure Service Fabric ausgeführte Anwendung verfügbar zu machen. Sie erstellen ein Docker-Image mit Ihrer Anwendung und Ihrem Apache Tomcat-Server, übertragen es mithilfe von Push an eine Containerregistrierung, erstellen eine Service Fabric-Containeranwendung und stellen diese bereit.
ms.topic: conceptual
ms.date: 6/08/2018
ms.author: pepogors
ms.openlocfilehash: 3de97bc277195dff2daf5868c0eb9aec5d6e27c0
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96534028"
---
# <a name="create-service-fabric-container-running-apache-tomcat-server-on-linux"></a>Erstellen eines auf einem Apache Tomcat-Server ausgeführten Service Fabric-Containers unter Linux
Apache Tomcat ist eine beliebte Open Source-Implementierung der Java Servlet- und Java Server-Technologien. In diesem Artikel wird gezeigt, wie Sie einen Container mit Apache Tomcat und eine einfache Webanwendung erstellen, den Container für einen Service Fabric-Cluster unter Linux bereitstellen und eine Verbindung mit der Webanwendung herstellen können.  

Weitere Informationen zu Apache Tomcat finden Sie auf der [Homepage von Apache Tomcat](https://tomcat.apache.org/). 

## <a name="prerequisites"></a>Voraussetzungen
* Ein Entwicklungscomputer, auf dem Folgendes ausgeführt wird:
  * [Service Fabric-SDK und -Tools](service-fabric-get-started-linux.md)
  * [Docker CE für Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Service Fabric-Befehlszeilenschnittstelle](service-fabric-cli.md)

* Eine Containerregistrierung in Azure Container Registry. Über [das Azure-Portal](../container-registry/container-registry-get-started-portal.md) oder [die Azure CLI](./service-fabric-tutorial-create-container-images.md#deploy-azure-container-registry) können Sie in Ihrem Azure-Abonnement eine Containerregistrierung erstellen. 

## <a name="build-a-tomcat-image-and-run-it-locally"></a>Erstellen und lokales Ausführen eines Tomcat-Image
Führen Sie die Schritte in diesem Abschnitt durch, um ein Docker-Image basierend auf einem Apache Tomcat-Image und eine einfache Web-App zu erstellen und diese dann in einem Container auf Ihrem lokalen System auszuführen. 
 
1. Klonen Sie das Beispielrepository [Erste Schritte mit Service Fabric mit Java](https://github.com/Azure-Samples/service-fabric-java-getting-started) auf Ihrem Entwicklungscomputer.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   ```

1. Ändern Sie die Verzeichnisse in das Beispielverzeichnis für den Apache Tomcat-Server (*service-fabric-java-getting-started/container-apache-tomcat-web-server-sample*):

   ```bash
   cd service-fabric-java-getting-started/container-apache-tomcat-web-server-sample
   ```

1. Erstellen Sie basierend auf dem offiziellen [Tomcat-Image](https://hub.docker.com/_/tomcat/) auf Docker Hub und dem Beispiel zum Tomcat-Server eine Docker-Datei. Erstellen Sie im Verzeichnis *service-fabric-java-getting-started/container-apache-tomcat-web-server-sample* eine Datei namens *Dockerfile* (ohne Dateierweiterung). Fügen Sie der *Dockerfile* Folgendes hinzu, und speichern Sie Ihre Änderungen:

   ```
   FROM library/tomcat

   EXPOSE 8080

   COPY ./ApacheTomcat /usr/local/tomcat
   ```

   Weitere Informationen finden Sie in der [Dockerfile-Referenz](https://docs.docker.com/engine/reference/builder/).


4. Melden Sie sich bei Docker an, und führen Sie den Befehl `docker build` aus, um das Image zu erstellen, mit dem Ihre Webanwendung ausgeführt wird:

   ```bash
   docker login
   docker build . -t tomcattest
   ```

   Dieser Befehl erstellt das neue Image gemäß den Anweisungen aus der Dockerfile-Datei. Das Image erhält den Namen `tomcattest`. Hierzu wird der Parameter „-t“ (für Tagging) verwendet. Zur Erstellung eines Containerimages wird zunächst das Basisimage von Docker Hub heruntergeladen, und die Anwendung wird hinzugefügt. 

   Wenn der Buildbefehl abgeschlossen ist, rufen Sie den Befehl `docker images` ab, um Informationen zum neuen Image anzuzeigen:

   ```bash
   $ docker images
    
   REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
   tomcattest                    latest              86838648aab6        2 minutes ago       194 MB
   ```

5. Stellen Sie sicher, dass Ihre Containeranwendung lokal ausgeführt wird, bevor Sie sie mithilfe von Push an die Containerregistrierung übertragen:
 
   ```bash
   docker run -itd --name tomcat-site -p 8080:8080 tomcattest.
   ```
   
   * `--name` versieht den Container mit einem Namen, sodass Sie statt über dessen ID mittels eines Anzeigenamens auf diesen verweisen können.
   * `-p` gibt die Portzuordnung zwischen dem Container und dem Hostbetriebssystem an. 

   > [!Note]
   > Der Port, den Sie mit dem Parameter `-p` öffnen, muss der Port sein, auf den Ihre Tomcat-Anwendung für Anforderungen lauscht. Im aktuellen Beispiel ist ein Connector in der Datei *ApacheTomcat/conf/server.xml* zum Lauschen auf Port 8080 für HTTP-Anforderungen konfiguriert. Dieser Port ist Port 8080 auf dem Host zugeordnet. 

   Weitere Informationen zu anderen Parametern finden Sie in der [Dokumentation zur Ausführung von Docker](https://docs.docker.com/engine/reference/commandline/run/).

1. Um den Container zu testen, öffnen Sie einen Browser, und geben Sie eine der folgenden URLs ein. Für jede URL wird eine Variante der Willkommensseite „Hallo Welt!“ angezeigt.

   - `http://localhost:8080/hello` 
   - `http://localhost:8080/hello/sayhello` 
   - `http://localhost:8080/hello/sayhi` 

   ![Hallo Welt! – /sayhi](./media/service-fabric-get-started-tomcat/hello.png)

2. Halten Sie den Container an, und löschen Sie ihn von Ihrem Entwicklungscomputer:

   ```bash
   docker stop tomcat-site
   docker rm tomcat-site
   ```

## <a name="push-the-tomcat-image-to-your-container-registry"></a>Übertragen des Tomcat-Images mithilfe von Push an Ihre Containerregistrierung
Nachdem Sie sichergestellt haben, dass das Tomcat-Image in einem Container auf Ihrem Entwicklungscomputer ausgeführt wird, pushen Sie es an ein Repository in einer Containerregistrierung. Dies dient zur [Reduzierung von Unterbrechungen](../container-registry/buffer-gate-public-content.md) Ihrer Workflows für die Imageentwicklung und -bereitstellung. In diesem Artikel wird das Image mit Azure Container Registry gespeichert, mit einigen Änderungen an den Schritten können Sie jedoch eine beliebige Containerregistrierung verwenden. In diesem Artikel wird vorausgesetzt, dass der Registrierungsname *myregistry* und der vollständige Registrierungsname „myregistry.azurecr.io“ lautet. Ändern Sie dies entsprechend in Bezug auf Ihr Szenario. 

1. Führen Sie `docker login` aus, um sich mit Ihren [Registrierungsanmeldeinformationen](../container-registry/container-registry-authentication.md) bei der Containerregistrierung anzumelden.

   Im folgenden Beispiel werden die ID und das Kennwort eines Azure Active Directory-[Dienstprinzipals](../active-directory/develop/app-objects-and-service-principals.md) übergeben. Angenommen, Sie haben Ihrer Registrierung für ein Automatisierungsszenario einen Dienstprinzipal zugewiesen. Alternativ können Sie sich mit Ihrem für die Registrierung verwendeten Benutzernamen und Kennwort anmelden.

   ```bash
   docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
   ```

2. Mit dem folgenden Befehl wird ein Tag oder Alias des Images mit einem vollqualifizierten Pfad zur Registrierung erstellt. In diesem Beispiel wird das Image im `samples`-Namespace platziert, um den Stamm der Registrierung nicht zu überladen.

   ```bash
   docker tag tomcattest myregistry.azurecr.io/samples/tomcattest
   ```

3. Übertragen des Image mithilfe von Push an Ihre Containerregistrierung:

   ```bash
   docker push myregistry.azurecr.io/samples/tomcattest
   ```

## <a name="build-and-deploy-the-service-fabric-container-application"></a>Erstellen und Bereitstellen der Service Fabric-Containeranwendung
Nachdem Sie das Tomcat-Image nun mithilfe von Push an eine Containerregistrierung übertragen haben, können Sie eine Service Fabric-Containeranwendung erstellen und bereitstellen, die das Tomcat-Image von Ihre Registrierung abruft und als Containerdienst in Ihrem Cluster ausführt. 

1. Erstellen Sie ein neues Verzeichnis außerhalb Ihres lokalen Klons (außerhalb der Verzeichnisstruktur *service-fabric-java-getting-started*). Wechseln Sie zu diesem, und erstellen Sie mithilfe von Yeoman ein Gerüst für eine Containeranwendung: 

   ```bash
   yo azuresfcontainer 
   ```
   Geben Sie bei Aufforderung die folgenden Werte ein:

   * Name Ihrer Anwendung: ServiceFabricTomcat
   * Name des Anwendungsdiensts: TomcatService
   * Name des Image: Geben Sie die URL für das Containerimage in Ihrer Containerregistrierung (z.B. „myregistry.azurecr.io/samples/tomcattest“) an.
   * Befehle: Lassen Sie dieses Feld leer. Da für dieses Image ein Workloadeinstiegspunkt definiert ist, müssen Sie nicht explizit Eingabebefehle angeben. (Befehle werden im Container ausgeführt, wodurch der Container nach dem Start weiter ausgeführt wird.)
   * Anzahl der Instanzen des Gastcontaineranwendung: 1

   ![Service Fabric-Yeoman-Generator für Container](./media/service-fabric-get-started-tomcat/yo-generator.png)

10. Fügen Sie im Dienstmanifest (*ServiceFabricTomcat/ServiceFabricTomcat/TomcatServicePkg/ServiceManifest.xml*) den folgenden XML-Code unter dem Stammtag **ServiceManfest** hinzu, um den Port zu öffnen, auf den Ihre Anwendung zurzeit für Anforderungen lauscht. Das Tag **Endpoint** deklariert das Protokoll und den Port für den Endpunkt. In diesem Artikel lauscht der Containerdienst auf Port 8080: 

   ```xml
   <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
       listen. Please note that if your service is partitioned, this port is shared with 
       replicas of different partitions that are placed in your code. -->
      <Endpoint Name="endpointTest" Port="8080" Protocol="tcp"/>
    </Endpoints>
   </Resources>
   ```

11. Fügen Sie im Anwendungsmanifest (*ServiceFabricTomcat/ServiceFabricTomcat/ApplicationManifest.xml*) unter dem Tag **ServiceManifestImport** den folgenden XML-Code hinzu. Ersetzen Sie **AccountName** und **Password** im Tag **RepositoryCredentials** durch den Namen Ihrer Containerregistrierung und das Kennwort, mit dem Sie sich bei dieser anmelden.

   ```xml
   <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="8080" EndpointRef="endpointTest"/>
      <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
    </ContainerHostPolicies>
   </Policies>
   ```

   Das Tag **ContainerHostPolicies** gibt Richtlinien für die Aktivierung der Containerhosts an.
    
   * Das Tag **PortBinding** konfiguriert die Richtlinie zur Zuordnung von Containerports mit Hosts. Das Attribut **ContainerPort** ist auf 8080 festgelegt, da der Container Port 8080 wie in der Dockerfile-Datei angegeben verfügbar macht. Das Attribut **EndpointRef** ist auf „endpointTest“ festgelegt, den im vorherigen Schritt im Dienstmanifest definierten Endpunkt. Anforderungen, die beim Dienst an Port 8080 eingehen, werden deshalb im Container dem Port 8080 zugeordnet. 
   * Das Tag **RepositoryCredentials** gibt die Anmeldeinformationen an, die der Container zur Authentifizierung beim (privaten) Repository benötigt, von dem er das Image abruft. Diese Richtlinie ist nicht erforderlich, wenn das Image aus einem öffentlichen Repository abgerufen wird.
    

12. Stellen Sie im Ordner *ServiceFabricTomcat* eine Verbindung mit dem Service Fabric-Cluster her. 

   * Um eine Verbindung mit dem lokalen Service Fabric-Cluster herzustellen, führen Sie Folgendes aus:

     ```bash
     sfctl cluster select --endpoint http://localhost:19080
     ```
    
   * Um eine Verbindung mit einem sicheren Azure-Cluster herzustellen, stellen Sie sicher, dass im Verzeichnis *ServiceFabricTomcat* das Clientzertifikat als PEM-Datei vorhanden ist, und führen Sie Folgendes aus: 

     ```bash
     sfctl cluster select --endpoint https://PublicIPorFQDN:19080 -pem your-certificate.pem -no-verify
     ```
     Ersetzen Sie im vorhergehenden Befehl `your-certificate.pem` durch den Namen Ihrer Clientzertifikatdatei. In Entwicklungs- und Testumgebungen wird häufig das Clusterzertifikat als Clientzertifikat verwendet. Wenn Ihr Zertifikat nicht selbst signiert ist, lassen Sie den Parameter `-no-verify` aus. 
       
     Clusterzertifikate werden in der Regel lokal als PFX-Dateien heruntergeladen. Wenn Ihr Zertifikat bereits im PEM-Format vorhanden ist, können Sie mit dem folgenden Befehl eine PEM-Datei anhand einer PFX-Datei erstellen:

     ```bash
     openssl pkcs12 -in your-certificate.pfx -out your-certificate.pem -nodes -passin pass:your-pfx-password
     ```

     Wenn Ihre PFX-Datei nicht kennwortgeschützt ist, verwenden Sie `-passin pass:` für den letzten Parameter.


13. Führen Sie das Installationsskript aus, das in der Vorlage für die Bereitstellung der Anwendung für Ihren Cluster zur Verfügung gestellt wurde. Das Skript kopiert das Anwendungspaket in den Clusterimagespeicher, registriert den Anwendungstyp und erstellt eine Instanz der Anwendung.

     ```bash
     ./install.sh
     ```

   Nachdem Sie das Installationsskript ausgeführt haben, öffnen Sie einen Browser, und navigieren Sie zum Service Fabric Explorer:
    
   * Verwenden Sie auf einem lokalen Cluster `http://localhost:19080/Explorer` (ersetzen Sie *localhost* durch die private IP-Adresse der VM, falls Sie Vagrant unter Mac OS X verwenden).
   * Verwenden Sie in einem sicheren Azure-Cluster `https://PublicIPorFQDN:19080/Explorer`. 
    
   Erweitern Sie den Knoten **Anwendungen**. Hier finden Sie nun einen Eintrag für Ihren Anwendungstyp **ServiceFabricTomcatType** und einen weiteren für die erste Instanz dieses Typs. Es kann ein paar Minuten dauern, bis die Bereitstellung durch die Anwendung abgeschlossen ist.

   ![Service Fabric Explorer](./media/service-fabric-get-started-tomcat/service-fabric-explorer.png)


1. Um auf dem Tomcat-Server auf die Anwendung zuzugreifen, öffnen Sie ein Browserfenster, und geben Sie eine der folgenden URLs ein. Wenn Sie eine Bereitstellung für den lokalen Cluster durchgeführt haben, verwenden Sie *localhost* für *PublicIPorFQDN*. Für jede URL wird eine Variante der Willkommensseite „Hallo Welt!“ angezeigt.

   * http://PublicIPorFQDN:8080/hello  
   * http://PublicIPorFQDN:8080/hello/sayhello
   * http://PublicIPorFQDN:8080/hello/sayhi

## <a name="clean-up"></a>Bereinigung
Verwenden Sie das Deinstallationsskript aus der Vorlage, um die Anwendungsinstanz aus Ihrem Cluster zu löschen und die Registrierung des Anwendungstyps aufzuheben.

```bash
./uninstall.sh
```

Wenn Sie das Image mithilfe von Push an die Containerregistrierung übertragen haben, können Sie das lokale Image von Ihrem Entwicklungscomputer löschen:

```
docker rmi tomcattest
docker rmi myregistry.azurecr.io/samples/tomcattest
```

## <a name="next-steps"></a>Nächste Schritte
* Um mehr über QuickSteps für zusätzliche Linux-Containerfeatures zu erfahren, lesen Sie [Erstellen Ihrer ersten Service Fabric-Containeranwendung unter Linux](service-fabric-get-started-containers-linux.md).
* Ausführlichere Informationen zu Schritten für Linux-Container finden Sie im [Tutorial: Erstellen von Containerimages für einen Linux-basierten Service Fabric-Cluster](service-fabric-tutorial-create-container-images.md).
* Weitere Informationen zum Ausführen von [Containern in Service Fabric](service-fabric-containers-overview.md)


