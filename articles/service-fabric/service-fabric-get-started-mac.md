---
title: Einrichten Ihrer Entwicklungsumgebung unter macOS
description: Installieren Sie Laufzeit, SDK und Tools, und erstellen Sie einen lokalen Entwicklungscluster. Nach Abschluss des Setups können Sie mit der Erstellung von Anwendungen unter macOS beginnen.
ms.topic: conceptual
ms.date: 10/16/2020
ms.custom: devx-track-js
ms.openlocfilehash: d08046c8f29901dd9650a1edc886efa2ff226e00
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93086776"
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Einrichten Ihrer Entwicklungsumgebung unter Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [Mac OS X](service-fabric-get-started-mac.md)

Sie können Azure Service Fabric-Anwendungen für die Ausführung in Linux-Clustern unter Mac OS X erstellen. In diesem Dokument erfahren Sie, wie Sie Ihren Mac für die Entwicklung einrichten.

## <a name="prerequisites"></a>Voraussetzungen
Azure Service Fabric wird nicht nativ unter Mac OS X ausgeführt. Für die Ausführung eines lokalen Service Fabric-Clusters wird ein vorkonfiguriertes Docker-Containerimage bereitgestellt. Bevor Sie beginnen, benötigen Sie Folgendes:

* Mindestens 4 GB RAM
* Neueste Version von [Docker](https://www.docker.com/)

>[!TIP]
>
>Führen Sie die Schritte in der [Docker-Dokumentation](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install) aus, um Docker auf Ihrem Mac zu installieren. [Überprüfen Sie anschließend die Installation](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine).
>

## <a name="create-a-local-container-and-set-up-service-fabric"></a>Erstellen eines lokalen Containers und Einrichten von Service Fabric
Führen Sie die folgenden Schritte aus, um einen lokalen Docker-Container einzurichten und darin einen Service Fabric-Cluster auszuführen:

1. Aktualisieren Sie die Konfiguration des Docker-Daemons auf dem Host mit den folgenden Einstellungen, und starten Sie den Docker-Daemon neu: 

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```
    Sie können diese Einstellungen direkt in der Datei „daemon.json“ im Docker-Installationspfad aktualisieren. Sie können die Daemon-Konfigurationseinstellungen direkt in Docker ändern. Klicken Sie auf das **Docker-Symbol** und anschließend auf **Einstellungen** > **Daemon** > **Erweitert**.
    
    >[!NOTE]
    >
    >Es wird empfohlen, den Daemon direkt in Docker zu ändern, da sich der Speicherort der Datei „daemon.json“ von Computer zu Computer unterscheiden kann. Beispiel: ~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json
    >

    >[!TIP]
    >Es empfiehlt sich, Docker beim Testen großer Anwendungen mehr Ressourcen zuzuordnen. Klicken Sie hierzu auf das **Docker-Symbol** und anschließend auf **Erweitert** , um die Anzahl von Kernen und die Speichermenge anzupassen.

2. Starten Sie den Cluster.<br/>
    <b>Ubuntu 18.04 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16.04 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u16
    ```

    >[!TIP]
    > Standardmäßig wird dann das Image mit der aktuellen Version von Service Fabric per Pullvorgang bereitgestellt. Informationen zu bestimmten Revisionen finden Sie auf der Seite [Service Fabric Onebox](https://hub.docker.com/_/microsoft-service-fabric-onebox) von Docker Hub.



3. Optional: Erstellen Sie das erweiterte Service Fabric-Image.

    Erstellen Sie zum Erstellen Ihres angepassten Images eine Datei namens `Dockerfile` in einem neuen Verzeichnis:

    >[!NOTE]
    >Sie können das Image oben mit einer Dockerfile-Datei anpassen, um in Ihrem Container weitere Programme oder Abhängigkeiten hinzuzufügen.
    >Wenn Sie beispielsweise `RUN apt-get install nodejs -y` hinzufügen, können Anwendungen vom Typ `nodejs` als ausführbare Gastdateien unterstützt werden.
    ```Dockerfile
    FROM mcr.microsoft.com/service-fabric/onebox:u18
    RUN apt-get install nodejs -y
    EXPOSE 19080 19000 80 443
    WORKDIR /home/ClusterDeployer
    CMD ["./ClusterDeployer.sh"]
    ```
    
    >[!TIP]
    > Standardmäßig wird dann das Image mit der aktuellen Version von Service Fabric per Pullvorgang bereitgestellt. Bestimmte Revisionen finden Sie auf der Seite [Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/).

    Öffnen Sie ein Terminal, wechseln Sie mit `cd` zum Verzeichnis mit Ihrer Datei vom Typ `Dockerfile`, und führen Sie anschließend Folgendes aus, um Ihr wiederverwendbares Images auf der Grundlage der Datei vom Typ `Dockerfile` zu erstellen:

    ```bash 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Dieser Vorgang dauert etwas, muss jedoch nur einmal ausgeführt werden.

    Nun können Sie bei Bedarf schnell eine lokale Kopie von Service Fabric starten, indem Sie Folgendes ausführen:

    ```bash 
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Geben Sie einen Namen für Ihre Containerinstanz an, um die Lesbarkeit zu verbessern. 
    >
    >Wenn Ihre Anwendung an bestimmten Ports lauscht, müssen diese mit zusätzlichen Tags vom Typ `-p` angegeben werden. Beispiel: Wenn Ihre Anwendung an Port 8080 lauscht, fügen Sie das folgende `-p`-Tag hinzu:
    >
    >`docker run -itd -p 19000:19000 -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:u18`
    >

4. Es dauert einen Moment, bis der Cluster gestartet wird. Wenn er ausgeführt wird, können Sie mithilfe des folgenden Befehls Protokolle anzeigen oder zum Dashboard wechseln, um sich über die Integrität der Cluster zu informieren: `http://localhost:19080`

    ```bash 
    docker logs sftestcluster
    ```


5. Zum Anhalten und Bereinigen des Containers verwenden Sie den folgenden Befehl. Sie verwenden diesen Container jedoch im nächsten Schritt.

    ```bash 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Bekannte Einschränkungen 
 
 Für den lokalen Cluster in einem Container auf einem Mac sind folgende Einschränkungen bekannt: 
 
 * Der DNS-Dienst wird nicht ausgeführt und wird zurzeit im Container nicht unterstützt. [Issue #132](https://github.com/Microsoft/service-fabric/issues/132)
 * Das Ausführen von containerbasierten Apps erfordert die Ausführung von SF auf einem Linux-Host. Geschachtelte Container-Apps werden derzeit nicht unterstützt.

## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>Einrichten der Service Fabric-Befehlszeilenschnittstelle (sfctl) auf Ihrem Mac

Befolgen Sie die Anweisungen unter [Service Fabric-Befehlszeilenschnittstelle](service-fabric-cli.md#cli-mac), um die Service Fabric-Befehlszeilenschnittstelle (`sfctl`) auf Ihrem Mac zu installieren.
Die Befehle der Befehlszeilenschnittstelle unterstützen die Interaktion mit Service Fabric-Entitäten wie Clustern, Anwendungen und Diensten.

1. Führen Sie den folgenden Befehl aus, um vor dem Bereitstellen von Anwendungen eine Verbindung mit dem Cluster herzustellen. 

```bash
sfctl cluster select --endpoint http://localhost:19080
```

## <a name="create-your-application-on-your-mac-by-using-yeoman"></a>Erstellen einer Anwendung auf Ihrem Mac mit Yeoman

Die Gerüstbautools von Service Fabric unterstützen Sie beim Erstellen einer Service Fabric-Anwendung über das Terminal unter Verwendung des Yeoman-Vorlagengenerators. Führen Sie die folgenden Schritte aus, um sicherzustellen, dass der Service Fabric-Yeoman-Vorlagengenerator auf dem Computer verwendet werden kann:

1. Node.js und Node Package Manager (NPM) müssen auf dem Mac installiert sein. Die Software kann wie folgt mithilfe von [HomeBrew](https://brew.sh/) installiert werden:

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. Installieren Sie den [Yeoman](https://yeoman.io/)-Vorlagengenerator auf dem Computer über NPM:

    ```bash
    npm install -g yo
    ```
3. Installieren Sie den gewünschten Yeoman-Generator gemäß der Anleitung in der [Dokumentation zu den ersten Schritten](service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables). Führen Sie zum Erstellen von Service Fabric-Anwendungen mit Yeoman die folgenden Schritte aus:

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. Erstellen Sie nach der Installation der Generatoren ausführbare Gastdateien oder Containerdienste, indem Sie `yo azuresfguest` bzw. `yo azuresfcontainer` ausführen.

5. Zur Erstellung einer Service Fabric-Java-Anwendung auf einem Mac müssen JDK 1.8 und Gradle auf dem Hostcomputer installiert sein. Die Software kann wie folgt mithilfe von [HomeBrew](https://brew.sh/) installiert werden: 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

    > [!IMPORTANT]
    > Aktuelle Versionen von `brew cask install java` installieren ggf. eine neuere Version des JDK.
    > Stellen Sie sicher, dass Sie JDK 8 installieren.

## <a name="deploy-your-application-on-your-mac-from-the-terminal"></a>Bereitstellen der Anwendung auf Ihrem Mac über das Terminal

Nachdem Sie Ihre Service Fabric-Anwendung erstellt und den Buildvorgang durchgeführt haben, können Sie sie mithilfe der [Service Fabric-Befehlszeilenschnittstelle](service-fabric-cli.md#cli-mac) bereitstellen:

1. Stellen Sie eine Verbindung mit dem Service Fabric-Cluster her, der unter der Containerinstanz auf Ihrem Mac ausgeführt wird:

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Führen Sie in Ihrem Projektverzeichnis das Installationsskript aus:

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-31-development"></a>Einrichten der .NET Core 3.1-Entwicklung

Installieren Sie das [.NET Core 3.1 SDK für Mac](https://www.microsoft.com/net/core#macos), um mit dem [Erstellen von Service Fabric-C#-Anwendungen](service-fabric-create-your-first-linux-application-with-csharp.md) zu beginnen. Pakete für Service Fabric-Anwendungen mit .NET Core werden auf NuGet.org gehostet.

## <a name="install-the-service-fabric-plug-in-for-eclipse-on-your-mac"></a>Installieren des Service Fabric-Plug-Ins für Eclipse auf Ihrem Mac

Azure Service Fabric stellt ein Plug-In für Eclipse Neon (oder höher) für die Java-IDE bereit. Das Plug-In vereinfacht den Prozess der Erstellung und Bereitstellung von Java-Diensten. Führen Sie [diese Schritte](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse) aus, um das Service Fabric-Plug-In für Eclipse zu installieren oder auf die aktuelle Version zu aktualisieren. Die anderen Schritte unter [Service Fabric-Plug-In für die Entwicklung von Eclipse-Java-Anwendungen](service-fabric-get-started-eclipse.md) gelten ebenfalls: Erstellen einer Anwendung, Hinzufügen eines Diensts zu einer Anwendung, Deinstallieren einer Anwendung usw.

Der letzte Schritt besteht im Instanziieren des Containers mit einem Pfad, der mit Ihrem Host gemeinsam verwendet wird. Für das Plug-In ist diese Art der Instanziierung erforderlich, damit es mit dem Docker-Container auf Ihrem Mac verwendet werden kann. Beispiel:

```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox mcr.microsoft.com/service-fabric/onebox:latest
```

Die Attribute sind wie folgt definiert:
* `/Users/sayantan/work/workspaces/mySFWorkspace` ist der vollqualifizierte Pfad des Arbeitsbereichs auf Ihrem Mac.
* `/tmp/mySFWorkspace` ist der Pfad innerhalb des Containers, dem der Arbeitsbereich zugeordnet werden soll.

>[!NOTE]
> 
>Wenn Ihr Name/Pfad für den Arbeitsbereich anders lautet, aktualisieren Sie diese Werte im Befehl `docker run`.
> 
>Falls Sie den Container mit einem anderen Namen als `sfonebox` starten, sollten Sie den Wert des Namens in der Datei „testclient.sh“ in Ihrer Service Fabric Actor-Java-Anwendung aktualisieren.
>

## <a name="next-steps"></a>Nächste Schritte
<!-- Links -->
* [Erstellen und Bereitstellen Ihrer ersten Service Fabric-Java-Anwendung unter Linux mithilfe von Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Erstellen und Bereitstellen Ihrer ersten Service Fabric-Java-Anwendung unter Linux mithilfe des Service Fabric-Plug-Ins für Eclipse](service-fabric-get-started-eclipse.md)
* [Erstellen eines Service Fabric-Clusters in Azure über das Azure-Portal](service-fabric-cluster-creation-via-portal.md)
* [Erstellen eines Service Fabric-Clusters in Azure mithilfe von Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Modellieren von Anwendungen in Service Fabric](service-fabric-application-model.md)
* [Verwalten von Anwendungen mit der Service Fabric-Befehlszeilenschnittstelle](service-fabric-application-lifecycle-sfctl.md)
* [Vorbereiten einer Linux-Entwicklungsumgebung unter Windows](service-fabric-local-linux-cluster-windows.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
