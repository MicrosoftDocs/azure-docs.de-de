---
title: Einrichten eines Azure Service Fabric-Linux-Clusters unter Windows | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie Service Fabric-Linux-Cluster einrichten, die auf Windows-Entwicklungscomputern ausgeführt werden. Dies ist insbesondere für die plattformübergreifende Entwicklung von Nutzen.
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: mfussell
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: e700250a6ebcdb82f99c1b460a510811d7ceb96c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60719939"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Einrichten eines Linux-Service Fabric-Clusters auf Ihrem Windows-Entwicklungscomputer

In diesem Dokument wird beschrieben, wie Sie einen lokalen Linux-Service Fabric-Cluster auf Windows-Entwicklungscomputern einrichten. Das Einrichten eines lokalen Linux-Clusters ist für das schnelle Testen von Anwendungen, die für Linux-Cluster konzipiert sind, aber auf einem Windows-Computer entwickelt werden.

## <a name="prerequisites"></a>Voraussetzungen
Linux-basierte Service Fabric-Cluster werden nicht nativ unter Windows ausgeführt. Für die Ausführung eines lokalen Service Fabric-Clusters wird ein vorkonfiguriertes Docker-Containerimage bereitgestellt. Bevor Sie beginnen, benötigen Sie Folgendes:

* Mindestens 4 GB RAM
* Neueste Version von [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Docker muss im Linux-Modus ausgeführt werden

>[!TIP]
> * Sie können die in der offiziellen Docker-[Dokumentation](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) beschriebenen Schritte ausführen, um Docker auf Ihrem Windows-Computer zu installieren. 
> * Nachdem Sie die Installation abgeschlossen haben, können Sie mit [diesen Schritten](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine) überprüfen, ob alles richtig installiert wurde.


## <a name="create-a-local-container-and-setup-service-fabric"></a>Erstellen eines lokalen Containers und Einrichten von Service Fabric
Führen Sie die folgenden Schritte in PowerShell aus, um einen lokalen Docker-Container einzurichten und darin einen Service Fabric-Cluster auszuführen:


1. Aktualisieren Sie wie folgt die Konfiguration des Docker-Daemons auf dem Host, und starten Sie den Docker-Daemon neu: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    Es wird empfohlen, zum Aktualisieren auf das Docker-Symbol und auf „Einstellungen“ > „Daemon“ > „Erweitert“ zu klicken und dann das Update auszuführen. Starten Sie dann den Docker-Daemon neu, damit die Änderungen wirksam werden. 

2. Erstellen Sie zur Erstellung Ihres Service Fabric-Images eine Datei namens `Dockerfile` in einem neuen Verzeichnis:

    ```Dockerfile
    FROM microsoft/service-fabric-onebox
    WORKDIR /home/ClusterDeployer
    RUN ./setup.sh
    #Generate the local
    RUN locale-gen en_US.UTF-8
    #Set environment variables
    ENV LANG=en_US.UTF-8
    ENV LANGUAGE=en_US:en
    ENV LC_ALL=en_US.UTF-8
    EXPOSE 19080 19000 80 443
    #Start SSH before running the cluster
    CMD /etc/init.d/ssh start && ./run.sh
    ```

    >[!NOTE]
    >Sie können diese Datei anpassen, um in Ihrem Container weitere Programme oder Abhängigkeiten hinzuzufügen.
    >Wenn Sie beispielsweise `RUN apt-get install nodejs -y` hinzufügen, können Anwendungen vom Typ `nodejs` als ausführbare Gastdateien unterstützt werden.
    
    >[!TIP]
    > Standardmäßig wird dann das Image mit der aktuellen Version von Service Fabric per Pullvorgang bereitgestellt. Bestimmte Revisionen finden Sie auf der Seite [Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/).

3. Öffnen Sie ein Terminal, wechseln Sie per `cd` zum Verzeichnis mit Ihrer Datei vom Typ `Dockerfile`, und führen Sie anschließend Folgendes aus, um Ihr wiederverwendbares Images auf der Grundlage der Datei vom Typ `Dockerfile` zu erstellen:

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Dieser Vorgang dauert etwas, muss jedoch nur einmal ausgeführt werden.

4. Nun können Sie bei Bedarf schnell eine lokale Kopie von Service Fabric starten, indem Sie Folgendes ausführen:

    ```powershell 
    docker run --name sftestcluster -d -v //var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Geben Sie einen Namen für Ihre Containerinstanz an, um die Lesbarkeit zu verbessern. 
    >
    >Wenn Ihre Anwendung an bestimmten Ports lauscht, müssen diese mit zusätzlichen Tags vom Typ `-p` angegeben werden. Beispiel: Wenn Ihre Anwendung an Port 8080 lauscht, fügen Sie das folgende `-p`-Tag hinzu:
    >
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox`
    >

5. Der Start des Clusters dauert einen Moment. Sie können mithilfe des folgenden Befehls Protokolle anzeigen oder zum Dashboard wechseln, um sich über die Integrität der Cluster zu informieren ([http://localhost:19080](http://localhost:19080)):

    ```powershell 
    docker logs sftestcluster
    ```

6. Nachdem Schritt 5 erfolgreich abgeschlossen wurde, können Sie von Ihrem Windows-Computer aus auf ``http://localhost:19080`` zugreifen und den Service Fabric Explorer anzeigen. Jetzt können Sie mit beliebigen Tools von Ihrem Windows-Entwicklungscomputer aus eine Verbindung mit diesem Cluster herstellen und Anwendungen für Linux-Service Fabric-Cluster bereitstellen. 

    > [!NOTE]
    > Das Eclipse-Plug-In wird unter Windows aktuell nicht unterstützt. 

7. Wenn Sie fertig sind, beenden und bereinigen Sie den Container mithilfe des folgenden Befehls:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Bekannte Einschränkungen 
 
 Für den lokalen Cluster in einem Container auf einem Mac sind folgende Einschränkungen bekannt: 
 
 * Der DNS-Dienst wird nicht ausgeführt und nicht unterstützt. ([Problem 132](https://github.com/Microsoft/service-fabric/issues/132))

## <a name="next-steps"></a>Nächste Schritte
* Erste Schritte mit [Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse)
* Weitere [Java-Beispiele](https://github.com/Azure-Samples/service-fabric-java-getting-started) anzeigen


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
