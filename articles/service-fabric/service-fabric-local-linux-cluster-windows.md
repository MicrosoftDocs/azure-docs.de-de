---
title: Einrichten eines Azure Service Fabric-Linux-Clusters unter Windows
description: In diesem Artikel wird beschrieben, wie Sie Service Fabric-Linux-Cluster einrichten, die auf Windows-Entwicklungscomputern ausgeführt werden. Dieser Ansatz ist für plattformübergreifende Entwicklung von Nutzen.
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 7b25a84e76773baea9f17430df1b7ba13aa661aa
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93087076"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Einrichten eines Linux-Service Fabric-Clusters auf Ihrem Windows-Entwicklungscomputer

In diesem Dokument wird beschrieben, wie Sie einen lokalen Linux-Service Fabric-Cluster auf einem Windows-Entwicklungscomputer einrichten. Das Einrichten eines lokalen Linux-Clusters ist für das schnelle Testen von Anwendungen, die für Linux-Cluster konzipiert sind, aber auf einem Windows-Computer entwickelt werden.

## <a name="prerequisites"></a>Voraussetzungen
Linux-basierte Service Fabric-Cluster werden nicht unter Windows ausgeführt. Um aber plattformübergreifende Prototypen zu ermöglichen, haben wir einen Docker-Clustercontainer von Linux-Service Fabric zur Verfügung gestellt, der über Docker für Windows bereitgestellt werden kann.

Bevor Sie beginnen, benötigen Sie Folgendes:

* Mindestens 4 GB RAM
* Neueste Version von [Docker für Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Docker muss im Linux-Containermodus ausgeführt werden.

>[!TIP]
> Führen Sie die Schritte in der [Docker-Dokumentation](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) aus, um Docker auf Ihrem Windows-Computer zu installieren. [Überprüfen Sie anschließend die Installation](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine).
>

## <a name="create-a-local-container-and-setup-service-fabric"></a>Erstellen eines lokalen Containers und Einrichten von Service Fabric
Führen Sie die folgenden Schritte aus, um einen lokalen Docker-Container einzurichten und einen Service Fabric-Cluster darin auszuführen:


1. Aktualisieren Sie wie folgt die Konfiguration des Docker-Daemons auf dem Host, und starten Sie den Docker-Daemon neu: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    Empfohlene Vorgehensweise zum Aktualisieren: 

    * Docker-Symbol > Einstellungen > Docker-Engine
    * Hinzufügen der oben aufgeführten neuen Felder
    * Anwenden und neu starten: Neustarten des Docker-Daemons, damit die Änderungen wirksam werden.

2. Starten des Clusters über PowerShell.<br/>
    <b>Ubuntu 18.04 LTS:</b>
    ```powershell
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16.04 LTS:</b>
    ```powershell
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

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Dieser Vorgang dauert etwas, muss jedoch nur einmal ausgeführt werden.

    Nun können Sie bei Bedarf schnell eine lokale Kopie von Service Fabric starten, indem Sie Folgendes ausführen:

    ```powershell 
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Geben Sie einen Namen für Ihre Containerinstanz an, um die Lesbarkeit zu verbessern. 
    >
    >Wenn Ihre Anwendung an bestimmten Ports lauscht, müssen diese mit zusätzlichen Tags vom Typ `-p` angegeben werden. Beispiel: Wenn Ihre Anwendung an Port 8080 lauscht, fügen Sie das folgende `-p`-Tag hinzu:
    >
    >`docker run -itd -p 19000:19000 -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:u18`
    >


4. Der Start des Clusters dauert einen Moment. Sie können mithilfe des folgenden Befehls Protokolle anzeigen oder zum Dashboard wechseln, um sich über die Integrität der Cluster zu informieren (`http://localhost:19080`):

    ```powershell 
    docker logs sftestcluster
    ```

5. Nachdem der Cluster wie in Schritt 4 beschrieben erfolgreich bereitgestellt wurde, können Sie auf Ihrem Windows-Computer zu ``http://localhost:19080`` navigieren, um das Service Fabric Explorer-Dashboard zu suchen. Jetzt können Sie mit Tools von Ihrem Windows-Entwicklungscomputer aus eine Verbindung mit diesem Cluster herstellen und Anwendungen für Linux-Service Fabric-Cluster bereitstellen. 

    > [!NOTE]
    > Das Eclipse-Plug-In wird unter Windows aktuell nicht unterstützt. 

6. Wenn Sie fertig sind, beenden und bereinigen Sie den Container mithilfe des folgenden Befehls:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Bekannte Einschränkungen 
 
 Für den lokalen Cluster in einem Container auf einem Mac sind folgende Einschränkungen bekannt: 
 
 * Der DNS-Dienst wird nicht ausgeführt und wird zurzeit im Container nicht unterstützt. [Issue #132](https://github.com/Microsoft/service-fabric/issues/132)
 * Das Ausführen von containerbasierten Apps erfordert die Ausführung von Service Fabric auf einem Linux-Host. Geschachtelte Container-Apps werden derzeit nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte
* [Erstellen und Bereitstellen Ihrer ersten Service Fabric-Java-Anwendung unter Linux mithilfe von Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* Erste Schritte mit [Eclipse](./service-fabric-get-started-eclipse.md)
* Weitere [Java-Beispiele](https://github.com/Azure-Samples/service-fabric-java-getting-started) anzeigen
* Informieren Sie sich über [Service Fabric-Supportoptionen](service-fabric-support.md).


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
