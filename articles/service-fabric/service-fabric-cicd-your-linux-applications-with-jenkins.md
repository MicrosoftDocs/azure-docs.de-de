---
title: Kontinuierliche Erstellung und Integration für Ihre Azure Service Fabric-Linux-Anwendungen mit Jenkins | Microsoft-Dokumentation
description: Kontinuierliche Erstellung und Integration für Ihre Service Fabric-Linux-Anwendung mit Jenkins
services: service-fabric
documentationcenter: java
author: sayantancs
manager: jpconnock
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/31/2018
ms.author: saysa
ms.openlocfilehash: 3b1e6f769d5c65065d95ac96c4ab4ed10702e5cf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61038752"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Verwenden von Jenkins zum Erstellen und Bereitstellen Ihrer Linux-Anwendungen
Jenkins ist ein beliebtes Tool für Continuous Integration und Continuous Deployment für Ihre Apps. Hier wird beschrieben, wie Sie Ihre Azure Service Fabric-Anwendung mit Jenkins erstellen und bereitstellen.

## <a name="topic-overview"></a>Themenübersicht
In diesem Artikel werden verschiedene Vorgehensweisen erläutert, um Ihre Jenkins-Umgebung einzurichten sowie Ihre Anwendung in einem Service Fabric-Cluster nach seiner Erstellung bereitzustellen. Führen Sie die folgenden allgemeinen Schritte aus, um erfolgreich Jenkins einrichten, Änderungen von GitHub abzurufen, Ihre Anwendung zu erstellen und diese für Ihren Cluster bereitzustellen:

1. Stellen Sie sicher, dass die [erforderlichen Komponenten](#prerequisites) installiert sind.
1. Befolgen Sie dann zum Einrichten von Jenkins die Schritte in einem dieser Abschnitte:
   * [Einrichten von Jenkins in einem Service Fabric-Cluster](#set-up-jenkins-inside-a-service-fabric-cluster) 
   * [Einrichten von Jenkins außerhalb eines Service Fabric-Clusters](#set-up-jenkins-outside-a-service-fabric-cluster)
   * [Installieren des Service Fabric-Plug-Ins in einer vorhandenen Jenkins-Umgebung](#install-service-fabric-plugin-in-an-existing-jenkins-environment)
1. Führen Sie nach der Einrichtung von Jenkins die Schritte unter [Erstellen und Konfigurieren eines Jenkins-Auftrags](#create-and-configure-a-jenkins-job) durch, um die Auslösung von Jenkins in GitHub einzurichten, wenn Änderungen an Ihrer Anwendung vorgenommen werden. Konfigurieren Sie mit diesen Schritten außerdem Ihre Jenkins-Auftragspipeline über den Buildschritt, um die Änderungen von GitHub abzurufen und Ihre Anwendung zu erstellen. 
1. Konfigurieren Sie abschließend für den Jenkins-Auftrag den Schritt für die Aktionen nach dem Erstellen, um die Anwendung für Ihren Service Fabric-Cluster bereitzustellen. Es gibt zwei Möglichkeiten, um Jenkins für die Bereitstellung Ihrer Anwendung für einen Cluster zu konfigurieren:    
   * Befolgen Sie für Entwicklungs- und Testumgebungen die Anweisungen unter [Konfigurieren der Bereitstellung mithilfe eines Clusterverwaltungsendpunkts](#configure-deployment-using-cluster-management-endpoint). Dies ist die Bereitstellungsmethode, die sich am einfachsten einrichten lässt.
   * Verwenden Sie für Produktionsumgebungen die Anweisungen unter [Konfigurieren einer Bereitstellung mithilfe von Azure-Anmeldeinformationen](#configure-deployment-using-azure-credentials). Microsoft empfiehlt die Verwendung dieser Methode für Produktionsumgebungen, da Sie mit Azure-Anmeldeinformationen den Zugriff, über den ein Jenkins-Auftrags bezüglich Ihrer Azure-Ressourcen verfügt, beschränken können. 

## <a name="prerequisites"></a>Voraussetzungen

- Stellen Sie sicher, dass Git lokal installiert ist. Sie können die entsprechende Git-Version über die [Git-Downloadseite](https://git-scm.com/downloads) für Ihr Betriebssystem installieren. Wenn Git neu für Sie ist, hilft Ihnen die [Git-Dokumentation](https://git-scm.com/docs) weiter.
- In diesem Artikel wird das Beispiel unter *Getting started with Service Fabric with Java* (Erste Schritte mit Service Fabric mit Java) auf GitHub verwendet: [https://github.com/Azure-Samples/service-fabric-java-getting-started](https://github.com/Azure-Samples/service-fabric-java-getting-started) für die zu erstellende und bereitzustellende Anwendung. Sie können dieses Repository forken oder mit einigen Änderungen an den Anweisungen Ihr eigenes GitHub-Projekt verwenden.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>Installieren des Service Fabric-Plug-Ins in einer vorhandenen Jenkins-Umgebung
Wenn Sie das Service Fabric-Plug-In zu einer vorhandenen Jenkins-Umgebung hinzufügen, benötigen Sie Folgendes:

- [Service Fabric-CLI](service-fabric-cli.md) (sfctl):

   > [!NOTE]
   > Installieren Sie die CLI auf Systemebene statt auf Benutzerebene, um Jenkins die Ausführung von CLI-Befehlen zu ermöglichen. 
   >

- Installieren Sie zum Bereitstellen von Java-Anwendungen sowohl [Gradle als auch Open JDK 8.0](service-fabric-get-started-linux.md#set-up-java-development). 
- Installieren Sie für die Bereitstellung von .NET Core 2.0-Anwendungen das [.NET Core 2.0 SDK](service-fabric-get-started-linux.md#set-up-net-core-20-development). 

Nachdem Sie die für Ihre Umgebung erforderlichen Komponenten installiert haben, können Sie das Azure Service Fabric-Plug-In im Jenkins Marketplace suchen und installieren.

Nachdem Sie das Plug-In installiert haben, fahren Sie mit [Erstellen und Konfigurieren eines Jenkins-Auftrags](#create-and-configure-a-jenkins-job) fort.


## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Einrichten von Jenkins in einem Service Fabric-Cluster

Sie können Jenkins innerhalb oder außerhalb eines Service Fabric-Clusters einrichten. Die folgenden Abschnitte zeigen, wie Sie Jenkins in einem Cluster einrichten, während Sie ein Azure-Speicherkonto zum Speichern des Status der Containerinstanz verwenden.

### <a name="prerequisites"></a>Voraussetzungen
- Sie benötigen einen Service Fabric-Linux-Cluster mit installiertem Docker-Dienst. Für die in Azure ausgeführten Service Fabric-Cluster muss bereits Docker installiert sein. Wenn Sie den Cluster lokal (OneBox-Entwicklungsumgebung) ausführen, überprüfen Sie mit dem Befehl `docker info`, ob Docker auf Ihrem Computer installiert ist. Falls nicht, können Sie es mit den folgenden Befehlen installieren:

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > Stellen Sie sicher, dass Port 8081 als benutzerdefinierter Endpunkt im Cluster angegeben ist. Wenn Sie einen lokalen Cluster verwenden, stellen Sie sicher, dass Port 8081 auf dem Hostcomputer geöffnet ist und über eine öffentliche IP-Adresse verfügt.
   >

### <a name="steps"></a>Schritte
1. Klonen Sie die Anwendung anhand der folgenden Befehle:
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

1. Behalten Sie den Status des Jenkins-Containers in einer Dateifreigabe bei:
   1. Erstellen Sie ein Azure-Speicherkonto in der **gleichen Region** wie der Cluster. Verwenden Sie beispielsweise einen Namen wie `sfjenkinsstorage1`.
   1. Erstellen Sie unter dem Speicherkonto eine **Dateifreigabe** mit einem Namen wie etwa `sfjenkins`.
   1. Klicken Sie für die Dateifreigabe auf **Verbinden**, und notieren Sie die unter **Herstellen einer Verbindung über Linux** angezeigten Werte. Der Wert sollte ungefähr so aussehen:

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > Zum Bereitstellen von cifs-Freigaben müssen Sie das „cifs-utils“-Paket auf den Clusterknoten installiert haben.      
   >

1. Aktualisieren Sie die Platzhalterwerte im `setupentrypoint.sh`-Skript mit den Details des Azure-Speichers aus Schritt 2.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Ersetzen Sie `[REMOTE_FILE_SHARE_LOCATION]` durch den Wert `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` aus der Ausgabe der Verbindung in Schritt 2 weiter oben.
   * Ersetzen Sie `[FILE_SHARE_CONNECT_OPTIONS_STRING]` durch den Wert `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` aus Schritt 2 oben.

1. **Nur sicherer Cluster**: 
   
   Damit die Bereitstellung von Anwendungen in einem sicheren Cluster über Jenkins konfiguriert werden kann, muss innerhalb des Jenkins-Containers auf das Clusterzertifikat zugegriffen werden können. Fügen Sie in der Datei *ApplicationManifest.xml* unter dem Tag **ContainerHostPolicies** diesen Zertifikatverweis hinzu, und aktualisieren Sie den Fingerabdruckwert mit dem des Clusterzertifikats.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Fügen Sie darüber hinaus in der Datei *ApplicationManifest.xml* die folgenden Zeilen unter dem (Stamm-)Tag **ApplicationManifest** hinzu, und aktualisieren Sie den Fingerabdruckwert mit dem des Clusterzertifikats.

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

1. Stellen Sie eine Verbindung mit dem Cluster her, und installieren Sie die Containeranwendung.

   **Sicherer Cluster**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   Im vorherigen Befehl wird das Zertifikat im PEM-Format übernommen. Wenn Ihr Zertifikat im PFX-Format vorliegt, können Sie es mit dem folgenden Befehl konvertieren. Wenn Ihre PFX-Datei nicht kennwortgeschützt ist, legen Sie als **passin**-Parameter `-passin pass:` fest.
   ```sh
   openssl pkcs12 -in cert.pfx -out cert.pem -nodes -passin pass:MyPassword1234!
   ``` 
   
   **Unsicherer Cluster**
   ```sh
   sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
   bash Scripts/install.sh
   ```

   Im Cluster wird ein Jenkins-Container installiert und kann mit dem Service Fabric Explorer überwacht werden.

   > [!NOTE]
   > Das Herunterladen des Jenkins-Images in den Cluster kann einige Minuten dauern.
   >

1. Navigieren Sie im Browser zu `http://PublicIPorFQDN:8081`. Hierdurch erhalten Sie den Pfad des ursprünglichen Administratorkennworts, das für die Anmeldung erforderlich ist. 
1. Ermitteln Sie in Service Fabric Explorer, auf welchem Knoten der Jenkins-Container ausgeführt wird. Melden Sie sich per SSH (Secure Shell) bei diesem Knoten an.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
1. Rufen Sie mithilfe von `docker ps -a` die ID der Containerinstanz ab.
1. Melden Sie sich per SSH (Secure Shell) beim Container an, und fügen Sie den Pfad aus dem Jenkins-Portal ein. Führen Sie beispielsweise folgende Befehle aus, wenn im Portal der Pfad `PATH_TO_INITIAL_ADMIN_PASSWORD` angezeigt wird:

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
1. Wählen Sie auf der Jenkins-Seite mit den ersten Schritten die Option „Select plugins to install“ (Zu installierende Plug-Ins auswählen), aktivieren Sie das Kontrollkästchen **None** (Keine), und klicken Sie auf „Install“ (Installieren).
1. Erstellen Sie einen Benutzer, oder wählen Sie die entsprechende Option, um den Vorgang als Administrator fortzusetzen.

Nachdem Sie Jenkins eingerichtet haben, fahren Sie mit [Erstellen und Konfigurieren eines Jenkins-Auftrags](#create-and-configure-a-jenkins-job) fort.  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Einrichten von Jenkins außerhalb eines Service Fabric-Clusters

Sie können Jenkins innerhalb oder außerhalb eines Service Fabric-Clusters einrichten. In den folgenden Abschnitten wird gezeigt, wie Sie die Einrichtung außerhalb eines Clusters durchführen.

### <a name="prerequisites"></a>Voraussetzungen
- Stellen Sie sicher, dass Docker auf Ihrem Computer installiert ist. Sie können die folgenden Befehle verwenden, um Docker über das Terminal zu installieren:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

  Wenn Sie `docker info` im Terminal ausführen, sollte die Ausgabe anzeigen, dass der Docker-Dienst ausgeführt wird.

### <a name="steps"></a>Schritte
1. Rufen Sie das Service Fabric-Jenkins-Containerimage per Pullvorgang ab: `docker pull rapatchi/jenkins:latest`. Dieses Image ist beim Service Fabric-Jenkins-Plug-In vorinstalliert.
1. Führen Sie das Containerimage aus: `docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
1. Rufen Sie die ID der Containerimageinstanz ab. Mit dem Befehl `docker ps –a` können Sie eine Liste mit allen Docker-Containern anzeigen.
1. Melden Sie sich mit den folgenden Schritten beim Jenkins-Portal an:

   1. Melden Sie sich über Ihren Host bei einer Jenkins-Shell an. Verwenden Sie die ersten vier Ziffern der Container-ID. Wenn die Container-ID z.B. `2d24a73b5964` lautet, verwenden Sie `2d24`.

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   1. Rufen Sie über die Jenkins-Shell das Administratorkennwort für Ihre Containerinstanz ab:

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   1. Um sich beim Jenkins-Dashboard anzumelden, rufen Sie die folgende URL in einem Webbrowser auf: `http://<HOST-IP>:8080`. Verwenden Sie das Kennwort aus dem vorherigen Schritt, um Jenkins zu entsperren.
   1. (Optional.) Nach der erstmaligen Anmeldung können Sie für die Durchführung der nachfolgenden Schritte ein eigenes Benutzerkonto erstellen oder weiterhin das Administratorkonto verwenden. Wenn Sie einen Benutzer erstellen, müssen Sie mit diesem Benutzer fortfahren.
1. Richten Sie GitHub für Jenkins ein, indem Sie die Schritte unter [Generating a new SSH key and adding it to the ssh-agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) (Generieren eines neuen SSH-Schlüssels und Hinzufügen des Schlüssels zum SSH-Agent) ausführen.
   * Generieren Sie mithilfe der Anweisungen von GitHub den SSH-Schlüssel, und fügen Sie ihn dem GitHub-Konto hinzu, unter dem das Repository gehostet wird.
   * Führen Sie die unter dem obigen Link beschriebenen Befehle in der Jenkins-Docker-Shell aus (nicht auf Ihrem Host).
   * Verwenden Sie den folgenden Befehl, um sich über Ihren Host an der Jenkins-Shell anzumelden:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Stellen Sie sicher, dass der Cluster oder der Computer, in bzw. auf dem das Jenkins-Containerimage gehostet wird, über eine öffentliche IP-Adresse verfügt. Die Jenkins-Instanz kann dann Benachrichtigungen von GitHub empfangen.

Nachdem Sie Jenkins eingerichtet haben, fahren Sie mit dem nächsten Abschnitt [Erstellen und Konfigurieren eines Jenkins-Auftrags](#create-and-configure-a-jenkins-job) fort.

## <a name="create-and-configure-a-jenkins-job"></a>Erstellen und Konfigurieren eines Jenkins-Auftrags

In den Schritten in diesem Abschnitt wird gezeigt, wie ein Jenkins-Auftrag konfiguriert wird, um auf Änderungen in einem GitHub-Repository zu reagieren, die Änderungen abzurufen und diese vorzunehmen. Am Ende dieses Abschnitts werden die abschließenden Schritte erläutert, um den Auftrag für die Bereitstellung Ihrer Anwendung basierend darauf zu konfigurieren, ob Sie eine Bereitstellung in einer Umgebung für Entwicklungen/Tests oder in einer Produktionsumgebung durchführen. 

1. Klicken Sie auf dem Jenkins-Dashboard auf **New Item** (Neues Element).
1. Geben Sie einen Elementnamen ein (z.B. **MyJob**). Wählen Sie die Option **free-style project** (Freestyleprojekt), und klicken Sie auf **OK**.
1. Die Seite „Job configuration“ (Auftragskonfiguration) wird geöffnet. (Um zur Konfiguration über das Jenkins-Dashboard zu gelangen, klicken Sie auf den Auftrag und dann auf **Configure** (Konfigurieren)).

1. Aktivieren Sie auf der Registerkarte **General** (Allgemein) das Kontrollkästchen **GitHub project** (GitHub-Projekt), und geben Sie die URL Ihres GitHub-Projekts an. Mit dieser URL wird die Service Fabric-Java-Anwendung gehostet, die Sie in den Jenkins-CI/CD-Flow (Continuous Integration, Continuous Deployment) integrieren möchten (z.B. `https://github.com/{your-github-account}/service-fabric-java-getting-started`).

1. Wählen Sie auf der Registerkarte **Source Code Management** (Quellcodeverwaltung) die Option **Git** aus. Geben Sie die Repository-URL an, unter der die Service Fabric-Java-Anwendung gehostet wird, die Sie in den Jenkins-CI/CD-Flow integrieren möchten (z.B. `https://github.com/{your-github-account}/service-fabric-java-getting-started`). Sie können auch den zu erstellenden Branch angeben (z.B. `/master`).
1. Konfigurieren Sie Ihr *GitHub*-Repository, um eine Verbindung mit Jenkins herzustellen:

   a. Navigieren Sie auf der Seite „GitHub repository“ (GitHub-Repository) zu **Settings** > **Integrations and Services** (Einstellungen > Integrationen und Dienste).

   b. Wählen Sie **Add Service** (Dienst hinzufügen), geben Sie **Jenkins** ein, und wählen Sie das **Jenkins-GitHub-Plug-In** aus.

   c. Geben Sie Ihre Jenkins-Webhook-URL ein. (Diese lautet standardmäßig `http://<PublicIPorFQDN>:8081/github-webhook/`.) Klicken Sie auf die Option zum **Hinzufügen/Aktualisieren des Diensts**.

   d. An die Jenkins-Instanz wird ein Testereignis gesendet. Für den Webhook in GitHub sollte ein grünes Häkchen angezeigt werden, und das Projekt sollte erstellt werden.

1. Wählen Sie in Jenkins auf der Registerkarte **Build Triggers** (Buildtrigger) die gewünschte Buildoption aus. In diesem Beispiel sollte ein Build immer ausgelöst werden, wenn eine Übertragung mithilfe von Push auf das Repository erfolgt. Wählen Sie daher **GitHub hook trigger for GITScm polling** (GitHub-Hooktrigger für den GITScm-Abruf) aus. (Früher hatte diese Option die Bezeichnung **Build when a change is pushed to GitHub** (Build bei Push einer Änderung auf GitHub durchführen).)
1. Führen Sie auf der Registerkarte **Build** einen der folgenden Schritte durch, je nachdem, ob Sie eine Java-Anwendung oder einer .NET Core-Anwendung erstellen:

   * **Bei Java-Anwendungen:** Wählen Sie aus der Dropdownliste **Add build step** (Buildschritt hinzufügen) die Option **Invoke Gradle Script** (Gradle-Skript aufrufen) aus. Klicken Sie auf **Erweitert**. Geben Sie im erweiterten Menü den Pfad zu **Root build script** (Stammbuildskript) für Ihre Anwendung an. „build.gradle“ wird am angegebenen Pfad abgerufen und entsprechend verwendet. Für die [ActorCounter-Anwendung](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter) lautet diese wie folgt: `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`.

     ![Service Fabric-Jenkins-Buildvorgang][build-step]

   * **Bei .NET Core-Anwendungen:** Wählen Sie aus der Dropdownliste **Add build step** (Buildschritt hinzufügen) die Option **Execute Shell** (Shell ausführen) aus. Im daraufhin angezeigten Befehlsfeld muss das Verzeichnis zunächst in den Pfad geändert werden, in dem sich die Datei „build.sh“ befindet. Nachdem das Verzeichnis geändert wurde, kann das Skript „build.sh“ ausgeführt werden, das die Anwendung erstellt.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     Im folgenden Screenshot finden Sie ein Beispiel für die Befehle, mit denen das Beispiel für den [Zählerdienst](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) mit dem Jenkins-Auftragsnamen „CounterServiceApplication“ erstellt wird.

      ![Service Fabric-Jenkins-Buildvorgang][build-step-dotnet]

1. Um Jenkins für die Bereitstellung Ihrer App in einem Service Fabric-Cluster in den Aktionen nach dem Erstellen zu konfigurieren, benötigen Sie den Speicherort des Clusterzertifikats im Jenkins-Container. Führen Sie einen der folgenden Schritte durch, je nachdem, ob Ihr Jenkins-Container innerhalb oder außerhalb des Clusters ausgeführt wird, und notieren Sie sich den Speicherort des Clusterzertifikats:

   * **Bei Jenkins-Ausführung innerhalb des Clusters:** Ermitteln Sie den Pfad zum Zertifikat, indem Sie den Wert der Umgebungsvariablen *Certificates_JenkinsOnSF_Code_MyCert_PEM* mittels Echo innerhalb des Containers zurückgeben.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **Bei Jenkins-Ausführung außerhalb des Clusters:** Befolgen Sie diese Schritte, um das Clusterzertifikat in Ihren Container zu kopieren:
     1. Ihr Zertifikat muss im PEM-Format vorliegen. Wenn keine PEM-Datei vorhanden ist, können Sie eine über die PFX-Zertifikatdatei erstellen. Wenn Ihre PFX-Datei nicht kennwortgeschützt ist, führen Sie den folgenden Befehl über Ihren Host aus:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
        ``` 

        Wenn die PFX-Datei kennwortgeschützt ist, schließen Sie das Kennwort im `-passin`-Parameter ein. Beispiel:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
        ``` 

     1. Führen Sie zum Abrufen der Container-ID für Ihren Jenkins-Container `docker ps` über Ihren Host aus.
     1. Kopieren Sie die PEM-Datei mit dem folgenden Docker-Befehl in Ihren Container:
    
        ```sh
        docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
        ``` 

Sie haben es fast geschafft! Lassen Sie den Jenkins-Auftrag geöffnet. Die letzte Aufgabe besteht darin, den Schritt nach dem Erstellen zu konfigurieren, um Ihre Anwendung für Ihren Service Fabric-Cluster bereitzustellen:

* Um eine Bereitstellung für eine Entwicklungs- oder Testumgebung durchzuführen, führen Sie die Schritte unter [Konfigurieren einer Bereitstellung mithilfe eines Clusterverwaltungsendpunkts](#configure-deployment-using-cluster-management-endpoint) durch.
* Um eine Bereitstellung für eine Produktionsumgebung durchzuführen, führen Sie die Schritte unter [Konfigurieren einer Bereitstellung mithilfe von Azure-Anmeldeinformationen](#configure-deployment-using-azure-credentials) durch.

## <a name="configure-deployment-using-cluster-management-endpoint"></a>Konfigurieren einer Bereitstellung mithilfe eines Clusterverwaltungsendpunkts
Für Entwicklungs- und Testumgebungen können Sie den Clusterverwaltungsendpunkt verwenden, um Ihre Anwendung bereitzustellen. Die Konfiguration einer Aktion nach dem Erstellen mit dem Clusterverwaltungsendpunkt für die Bereitstellung Ihrer Anwendung erfordert den geringsten Einrichtungsaufwand. Wenn Sie eine Bereitstellung für eine Produktionsumgebung durchführen, fahren Sie mit dem Abschnitt [Konfigurieren einer Bereitstellung mithilfe von Azure-Anmeldeinformationen](#configure-deployment-using-azure-credentials) fort, um einen Azure Active Directory-Dienstprinzipal zur Verwendung bei der Bereitstellung zu konfigurieren.    

1. Klicken Sie im Jenkins-Auftrag auf die Registerkarte **Post-build Actions** (Aktionen nach dem Erstellen). 
1. Wählen Sie in der Dropdownliste **Post-Build Actions** (Aktionen nach dem Erstellen) die Option **Deploy Service Fabric Project** (Service Fabric-Projekt bereitstellen). 
1. Aktivieren Sie unter **Service Fabric Cluster Configuration** (Service Fabric-Clusterkonfiguration) das Optionsfeld **Fill the Service Fabric Management Endpoint** (Service Fabric-Verwaltungsendpunkt füllen).
1. Geben Sie für **Management Host** (Verwaltungshost) den Verbindungsendpunkt für Ihren Cluster ein, z.B. `{your-cluster}.eastus.cloudapp.azure.com`.
1. Geben Sie für **Client Key** (Clientschlüssel) und **Client Cert** (Clientzertifikat) den Speicherort der PEM-Datei in Ihrem Jenkins-Container ein, z.B. `/var/jenkins_home/clustercert.pem`. (Im letzten Schritt des Abschnitts [Erstellen und Konfigurieren eines Jenkins-Auftrags](#create-and-configure-a-jenkins-job) haben Sie den Speicherort des Zertifikats kopiert.)
1. Konfigurieren Sie unter **Application Configuration** (Anwendungskonfiguration) die Felder **Application Name** (Anwendungsname), **Application Type** (Anwendungstyp) und den (relativen) **Path to Application Manifest** (Pfad zum Anwendungsmanifest).

   ![Service Fabric-Konfiguration in Jenkins – Aktion nach dem Erstellen: Konfigurieren eines Verwaltungsendpunkts](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

1. Klicken Sie auf **Verify Configuration**. Klicken Sie nach erfolgreicher Überprüfung auf **Save** (Speichern). Ihre Jenkins-Auftragspipeline ist nun vollständig konfiguriert. Fahren Sie mit dem Abschnitt [Nächste Schritte](#next-steps) fort, um Ihre Bereitstellung zu testen.

## <a name="configure-deployment-using-azure-credentials"></a>Konfigurieren einer Bereitstellung mithilfe von Azure-Anmeldeinformationen
Für Produktionsumgebungen wird ausdrücklich die Konfiguration von Azure-Anmeldeinformationen zur Bereitstellung Ihrer Anwendung empfohlen. In diesem Abschnitt wird gezeigt, wie auf der Registerkarte „Post-build Actions“ (Aktionen nach dem Erstellen) ein Azure Active Directory-Dienstprinzipal zum Bereitstellen ihrer Anwendung konfiguriert wird. Sie können Dienstprinzipalen Rollen in Ihrem Verzeichnis zuweisen, um die Berechtigungen des Jenkins-Auftrags zu beschränken. 

Für Entwicklungs- und Testumgebungen können Sie Azure-Anmeldeinformationen konfigurieren oder den Clusterverwaltungsendpunkt verwenden, um Ihre Anwendung bereitzustellen. Ausführliche Informationen zum Konfigurieren eines Clusterverwaltungsendpunkts finden Sie unter [Konfigurieren einer Bereitstellung mithilfe eines Clusterverwaltungsendpunkts](#configure-deployment-using-cluster-management-endpoint).   

1. Um einen Azure Active Directory-Dienstprinzipal zu erstellen und diesem Berechtigungen in Ihrem Azure-Abonnement zuzuweisen, führen Sie die Schritte unter [Erstellen einer Azure Active Directory-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff mithilfe des Portals](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) durch. Berücksichtigen Sie dabei folgende Hinweise:

   * Achten Sie darauf, die folgenden Werte zu kopieren und zu speichern, während Sie die Schritte im Thema befolgen: *Anwendungs-ID*, *Anwendungsschlüssel*, *Verzeichnis-ID (Mandanten-ID)* und *Abonnement-ID*. Sie benötigen diese für die Konfiguration der Azure-Anmeldeinformationen in Jenkins.
   * Wenn Sie keine der [erforderlichen Berechtigungen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) für Ihr Verzeichnis besitzen, müssen Sie sich an einen Administrator wenden, der Ihnen diese Berechtigungen gewähren oder den Dienstprinzipal für Sie erstellen kann. Anderenfalls müssen Sie den Verwaltungsendpunkt für Ihren Cluster auf der Registerkarte **Post-Build Actions** (Aktionen nach dem Erstellen) für Ihren Auftrag in Jenkins konfigurieren.
   * Im Abschnitt [Erstellen einer Azure Active Directory-Anwendung](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application) können Sie eine beliebige wohlgeformte URL für die **Anmelde-URL** eingeben.
   * Im Abschnitt [Zuweisen einer Anwendung zur Rolle](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) können Sie Ihrer Anwendung die *Leserolle* für die Ressourcengruppe für Ihren Cluster zuweisen.

1. Klicken Sie zurück im Jenkins-Auftrag auf die Registerkarte **Post-build Actions** (Aktionen nach dem Erstellen).
1. Wählen Sie in der Dropdownliste **Post-Build Actions** (Aktionen nach dem Erstellen) die Option **Deploy Service Fabric Project** (Service Fabric-Projekt bereitstellen). 
1. Aktivieren Sie unter **Service Fabric Cluster Configuration** (Service Fabric-Clusterkonfiguration) das Optionsfeld **Select the Service Fabric Cluster** (Service Fabric-Cluster auswählen). Klicken Sie neben **Azure Credentials** (Azure-Anmeldeinformationen) auf **Add** (Hinzufügen). Klicken Sie auf **Jenkins**, um den Jenkins-Anmeldeinformationsanbieter auszuwählen.
1. Wählen Sie im Jenkins-Anmeldeinformationsanbieter aus der Dropdownliste **Kind** (Art) die Option **Microsoft Azure Service Principal** (Microsoft Azure-Dienstprinzipal) aus.
1. Verwenden Sie die Werte, die Sie beim Festlegen Ihres Dienstprinzipals in Schritt 1 gespeichert haben, um die folgenden Felder festzulegen:

   * **Client-ID**: *Anwendungs-ID*:
   * **Geheimer Clientschlüssel**: *Anwendungsschlüssel*:
   * **Mandanten-ID**: *Verzeichnis-ID*:
   * **Abonnement-ID**: *Abonnement-ID*
1. Geben Sie eine beschreibende **ID** für die Auswahl der Anmeldeinformationen unter Jenkins sowie eine kurze **Description** (Beschreibung) ein. Klicken Sie dann auf **Verify Service Principal** (Dienstprinzipal überprüfen). Wenn die Überprüfung erfolgreich ist, klicken Sie auf **Add** (Hinzufügen).

   ![Service Fabric-Konfiguration in Jenkins: Eingabe von Azure-Anmeldeinformationen](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
1. Stellen Sie unter **Service Fabric Cluster Configuration** (Service Fabric-Clusterkonfiguration) sicher, dass Ihre neuen Anmeldeinformationen für **Azure Credentials** (Azure-Anmeldeinformationen) ausgewählt sind. 
1. Wählen Sie aus der Dropdownliste **Resource Group** (Ressourcengruppe) die Ressourcengruppe des Clusters aus, der für die Anwendung bereitgestellt werden soll.
1. Wählen Sie aus der Dropdownliste **Service Fabric** den Cluster aus, der für die Anwendung bereitgestellt werden soll.
1. Geben Sie für **Client Key** (Clientschlüssel) und **Client Cert** (Clientzertifikat) den Speicherort der PEM-Datei in Ihrem Jenkins-Container ein. Beispiel: `/var/jenkins_home/clustercert.pem`. 
1. Konfigurieren Sie unter **Application Configuration** (Anwendungskonfiguration) die Felder **Application Name** (Anwendungsname), **Application Type** (Anwendungstyp) und den (relativen) **Path to Application Manifest** (Pfad zum Anwendungsmanifest).
    ![Service Fabric-Jenkins-Aktion nach der Erstellung zum Konfigurieren eines Verwaltungsendpunkts](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
1. Klicken Sie auf **Verify Configuration**. Klicken Sie nach erfolgreicher Überprüfung auf **Save** (Speichern). Ihre Jenkins-Auftragspipeline ist nun vollständig konfiguriert. Fahren Sie mit dem Abschnitt [Nächste Schritte](#next-steps) fort, um Ihre Bereitstellung zu testen.

## <a name="troubleshooting-the-jenkins-plugin"></a>Problembehandlung beim Jenkins-Plug-In

Wenn bei den Jenkins-Plug-Ins Fehler auftreten, melden Sie das Problem auf der [Jenkins-JIRA-Seite](https://issues.jenkins-ci.org/) für die jeweilige Komponente.

## <a name="next-steps"></a>Nächste Schritte
GitHub und Jenkins sind jetzt konfiguriert. Bei Bedarf können Sie im Projekt `reliable-services-actor-sample/Actors/ActorCounter` in Ihrem Fork des Repositorys https://github.com/Azure-Samples/service-fabric-java-getting-started einige Änderungen am Beispiel vornehmen. Übertragen Sie Ihre Änderungen mithilfe von Push auf den Remotebranch `master` (oder einen beliebigen anderen Branch, den Sie zur Verwendung konfiguriert haben). Hierdurch wird der Jenkins-Auftrag `MyJob` ausgelöst, den Sie konfiguriert haben. Die Änderungen werden von GitHub abgerufen, die Erstellung wird durchgeführt, und die Anwendung wird für den Clusterendpunkt bereitgestellt, den Sie auf der Registerkarte „Post-build Actions“ (Aktionen nach dem Erstellen) angegeben haben.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png

