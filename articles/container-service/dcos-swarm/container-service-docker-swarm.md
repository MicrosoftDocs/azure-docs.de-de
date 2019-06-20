---
title: (VERALTET) Verwalten von Azure-Swarm-Clustern mit der Docker-API
description: Bereitstellen von Containern für einen Docker Swarm-Cluster in Azure Container Service
services: container-service
author: rgardler
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 09/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 04cc9048271d653bd77fd7f2707c8f510ea8c29f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61456552"
---
# <a name="deprecated-container-management-with-docker-swarm"></a>(VERALTET) Containerverwaltung mit Docker Swarm

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Docker Swarm bietet eine Umgebung zum Bereitstellen von in Containern enthaltenen Workloads für Docker-Hosts in einem Pool. Für Docker Swarm wird die native Docker-API verwendet. Der Workflow zum Verwalten von Containern in Docker Swarm ist fast mit dem Workflow auf einem einzelnen Containerhost identisch. Dieses Dokument enthält einfache Beispiele für die Bereitstellung von in Containern enthaltenen Workloads in einer Azure Container Service-Instanz von Docker Swarm. Eine ausführlichere Dokumentation zu Docker Swarm finden Sie unter [Docker Swarm auf Docker.com](https://docs.docker.com/swarm/).

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Voraussetzungen für die Übungen in diesem Dokument:

[Bereitstellen eines Azure Container Service-Clusters](container-service-deployment.md)

[Verbinden mit einem Azure Container Service-Cluster](../container-service-connect.md)

## <a name="deploy-a-new-container"></a>Bereitstellen eines neuen Containers
Verwenden Sie zum Bereitstellen eines neuen Containers in Docker Swarm den Befehl `docker run` . (Stellen Sie dabei sicher, dass Sie gemäß den obigen Voraussetzungen einen SSH-Tunnel zu den Mastern geöffnet haben.) In diesem Beispiel wird ein Container aus dem Image `yeasy/simple-web` erstellt:

```bash
user@ubuntu:~$ docker run -d -p 80:80 yeasy/simple-web

4298d397b9ab6f37e2d1978ef3c8c1537c938e98a8bf096ff00def2eab04bf72
```

Verwenden Sie nach dem Erstellen des Containers den Befehl `docker ps` , um Informationen über den Container zurückzugeben. Beachten Sie hierbei, dass der Swarm-Agent, der den Container hostet, aufgelistet ist:

```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   31 seconds ago      Up 9 seconds        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

Sie können nun auf die in diesem Container ausgeführte Anwendung über den öffentlichen DNS-Namen des Swarm-Agent-Lastenausgleichs zugreifen. Diese Informationen finden Sie im Azure-Portal:  

![Echte Ergebnisse](./media/container-service-docker-swarm/real-visit.jpg)  

Standardmäßig sind für den Load Balancer die Ports 80, 8080 und 443 geöffnet. Wenn Sie die Verbindung über einen anderen Port herstellen möchten, müssen Sie den Port im Azure Load Balancer für den Agent-Pool öffnen.

## <a name="deploy-multiple-containers"></a>Bereitstellen von mehreren Containern
Wenn mehrere Container gestartet werden, können Sie durch das mehrfache Ausführen von „docker run“ den Befehl `docker ps` verwenden, um zu ermitteln, auf welchen Hosts die Container ausgeführt werden. Im Beispiel unten sind die Container gleichmäßig auf die drei Swarm-Agents verteilt:  

```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
11be062ff602        yeasy/simple-web    "/bin/sh -c 'python i"   11 seconds ago      Up 10 seconds       10.0.0.6:83->80/tcp   swarm-agent-34A73819-2/clever_banach
1ff421554c50        yeasy/simple-web    "/bin/sh -c 'python i"   49 seconds ago      Up 48 seconds       10.0.0.4:82->80/tcp   swarm-agent-34A73819-0/stupefied_ride
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   2 minutes ago       Up 2 minutes        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

## <a name="deploy-containers-by-using-docker-compose"></a>Bereitstellen von Containern mithilfe von Docker Compose
Sie können Docker Compose verwenden, um die Bereitstellung und Konfiguration mehrerer Container zu automatisieren. Stellen Sie hierfür sicher, dass ein Secure Shell (SSH)-Tunnel erstellt wurde und dass die DOCKER_HOST-Variable festgelegt wurde (siehe Voraussetzungen oben).

Erstellen Sie auf Ihrem lokalen System die Datei „docker-compose.yml“. Verwenden Sie hierfür dieses [Beispiel](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/docker-compose.yml).

```bash
web:
  image: adtd/web:0.1
  ports:
    - "80:80"
  links:
    - rest:rest-demo-azure.marathon.mesos
rest:
  image: adtd/rest:0.1
  ports:
    - "8080:8080"

```

Führen Sie `docker-compose up -d` aus, um die Containerbereitstellungen zu starten:

```bash
user@ubuntu:~/compose$ docker-compose up -d
Pulling rest (adtd/rest:0.1)...
swarm-agent-3B7093B8-0: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-3: Pulling adtd/rest:0.1... : downloaded
Creating compose_rest_1
Pulling web (adtd/web:0.1)...
swarm-agent-3B7093B8-3: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-0: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/web:0.1... : downloaded
Creating compose_web_1
```

Anschließend wird eine Liste mit den ausgeführten Containern zurückgegeben. Diese Liste enthält die Container, die mithilfe von Docker Compose bereitgestellt wurden:

```bash
user@ubuntu:~/compose$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                     NAMES
caf185d221b7        adtd/web:0.1        "apache2-foreground"   2 minutes ago       Up About a minute   10.0.0.4:80->80/tcp       swarm-agent-3B7093B8-0/compose_web_1
040efc0ea937        adtd/rest:0.1       "catalina.sh run"      3 minutes ago       Up 2 minutes        10.0.0.4:8080->8080/tcp   swarm-agent-3B7093B8-0/compose_rest_1
```

Sie können natürlich auch `docker-compose ps` verwenden, um nur die Container zu untersuchen, die in der Datei `compose.yml` definiert sind.

## <a name="next-steps"></a>Nächste Schritte
[Weitere Informationen zu Docker Swarm](https://docs.docker.com/swarm/)

