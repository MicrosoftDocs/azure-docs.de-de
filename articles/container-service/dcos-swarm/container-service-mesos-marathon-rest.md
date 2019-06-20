---
title: (VERALTET) Verwalten eines Azure-DC/OS-Clusters mit der Marathon-REST-API
description: Stellen Sie mithilfe der Marathon-REST-API Container für einen Azure Container Service-DC/OS-Cluster bereit.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 73fa9c4433a2af780798f0439c0a119bc32a678f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64916699"
---
# <a name="deprecated-dcos-container-management-through-the-marathon-rest-api"></a>(VERALTET) DC/OS-Containerverwaltung über die Marathon-REST-API

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS stellt eine Umgebung für die Bereitstellung und Skalierung geclusterter Workloads bereit und abstrahiert die zugrunde liegende Hardware. Zusätzlich zu DC/OS ist auch ein Framework vorhanden, mit dem die Planung und Ausführung von Computeworkloads verwaltet wird. Es sind zwar Frameworks für viele gängige Workloads verfügbar, dieses Dokument bietet jedoch eine Einführung in das Erstellen und Skalieren von Containerbereitstellungen mithilfe der Marathon-REST-API. 

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie diese Beispiele durcharbeiten, benötigen Sie einen DC/OS-Cluster, der im Azure Container Service konfiguriert ist. Sie müssen auch über Remoteverbindungen mit diesem Cluster verfügen. Weitere Informationen zu diesen Elementen finden Sie in den folgenden Artikeln:

* [Bereitstellen eines Azure Container Service-Clusters](container-service-deployment.md)
* [Verbinden mit einem Azure Container Service-Cluster](../container-service-connect.md)

## <a name="access-the-dcos-apis"></a>Zugriff auf die DC/OS-APIs
Nachdem die Verbindung mit dem Azure Container Service-Cluster hergestellt wurde, können Sie auf DC/OS und die verwandten REST-APIs über „http:\//localhost:local-port“ zugreifen. Bei den Beispielen in diesem Dokument wird davon ausgegangen, dass das Tunneling über Port 80 erfolgt. Der Marathon-Endpunkt kann z. B. unter URIs erreicht werden, die mit „http:\//localhost/marathon/v2/“ beginnen. 

Weitere Informationen zu den verschiedenen APIs finden Sie in der Mesosphere-Dokumentation für die [Marathon-API](https://mesosphere.github.io/marathon/docs/rest-api.html) und die [Chronos-API](https://mesos.github.io/chronos/docs/api.html) sowie in der Apache-Dokumentation für die [Mesos Scheduler-API](https://mesos.apache.org/documentation/latest/scheduler-http-api/).

## <a name="gather-information-from-dcos-and-marathon"></a>Erfassen von Informationen von DC/OS und Marathon
Erfassen Sie vor dem Bereitstellen von Containern im DC/OS-Cluster einige Informationen zum DC/OS-Cluster, z.B. die Namen und den Status der DC/OS-Agents. Fragen Sie dazu den `master/slaves`-Endpunkt der DC/OS-REST-API ab. Wenn alles geklappt hat, gibt die Abfrage eine Liste mit DC/OS-Agents und jeweils einigen Eigenschaften zurück.

```bash
curl http://localhost/mesos/master/slaves
```

Verwenden Sie nun den Marathon-`/apps`-Endpunkt, um aktuelle Anwendungsbereitstellungen im DC/OS-Cluster zu überprüfen. Im Falle eines neuen Clusters wird ein leeres Array für Apps angezeigt.

```bash
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Bereitstellen eines Containers im Docker-Format
Sie stellen Container im Docker-Format mit der Marathon-REST-API über eine JSON-Datei bereit, die die vorgesehene Bereitstellung beschreibt. Im folgenden Beispiel wird ein Nginx-Container für einen privaten Agent im Cluster bereitgestellt. 

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Speichern Sie zum Bereitstellen eines Containers im Docker-Format die JSON-Datei an einem zugänglichen Speicherort. Führen Sie als Nächstes den folgenden Befehl aus, um den Container bereitzustellen. Geben Sie den Namen der JSON-Datei an (in diesem Beispiel: `marathon.json`).

```bash
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

Die Ausgabe sieht in etwa wie folgt aus:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Wenn Sie jetzt Marathon auf Anwendungen abfragen, erscheint diese neue Anwendung in der Ausgabe.

```bash
curl localhost/marathon/v2/apps
```

## <a name="reach-the-container"></a>Zugreifen auf den Container

Sie können überprüfen, ob Nginx in einem Container auf einem der privaten Agents im Cluster ausgeführt wird. Um den Host und Port zu ermitteln, auf denen der Container ausgeführt wird, fragen Sie Marathon nach den ausgeführten Aufgaben ab: 

```bash
curl localhost/marathon/v2/tasks
```

Ermitteln Sie in der Ausgabe den Wert von `host` (eine IP-Adresse wie `10.32.0.x`) und den Wert von `ports`.


Stellen Sie jetzt eine SSH-Terminalverbindung (keine getunnelte Verbindung) mit dem Verwaltungs-FQDN des Clusters her. Führen Sie nach dem Herstellen der Verbindung die folgende Anforderung aus, wobei Sie für `host` und `ports` die richtigen Werte angeben:

```bash
curl http://host:ports
```

Die Ausgabe des Nginx-Servers sieht in etwa wie folgt aus:

![Nginx von Container](./media/container-service-mesos-marathon-rest/nginx.png)




## <a name="scale-your-containers"></a>Skalieren der Container
Sie können die Marathon-API verwenden, um Anwendungsbereitstellungen horizontal hoch- oder herunterzuskalieren. Im vorherigen Beispiel haben Sie eine Instanz einer Anwendung bereitgestellt. Wir führen hierfür jetzt das horizontale Hochskalieren auf drei Instanzen einer Anwendung durch. Hierzu erstellen Sie eine JSON-Datei, indem Sie den folgenden JSON-Text verwenden und an einem zugänglichen Speicherort speichern.

```json
{ "instances": 3 }
```

Führen Sie über die getunnelte Verbindung folgenden Befehl aus, um die Anwendung horizontal hochzuskalieren.

> [!NOTE]
> Der URI lautet „http:\//localhost/marathon/v2/apps/“, gefolgt von der ID der zu skalierenden Anwendung. Wenn Sie das hier bereitgestellte Nginx-Beispiel verwenden, lautet der URI „http:\//localhost/marathon/v2/apps/nginx“.

```bash
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Fragen Sie den Marathon-Endpunkt zum Schluss auf Anwendungen ab. Wie Sie sehen, sind nun drei Nginx-Container vorhanden.

```bash
curl localhost/marathon/v2/apps
```

## <a name="equivalent-powershell-commands"></a>Entsprechende PowerShell-Befehle
Sie können diese Aktionen auch durchführen, indem Sie PowerShell-Befehle in einem Windows-System verwenden.

Führen Sie den folgenden Befehl aus, um Informationen zum DC/OS-Cluster zu erfassen, z.B. Agent-Namen und -Status:

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Sie stellen Container im Docker-Format mit Marathon über eine JSON-Datei bereit, die die vorgesehene Bereitstellung beschreibt. Im folgenden Beispiel wird der Nginx-Container bereitgestellt. Dabei wird Port 80 des DC/OS-Agents an Port 80 des Containers gebunden.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Speichern Sie zum Bereitstellen eines Containers im Docker-Format die JSON-Datei an einem zugänglichen Speicherort. Führen Sie als Nächstes den folgenden Befehl aus, um den Container bereitzustellen. Geben Sie den Pfad zur JSON-Datei an (in diesem Beispiel `marathon.json`).

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Sie können auch die Marathon-API verwenden, um Anwendungsbereitstellungen horizontal hoch- oder herunterzuskalieren. Im vorherigen Beispiel haben Sie eine Instanz einer Anwendung bereitgestellt. Wir führen hierfür jetzt das horizontale Hochskalieren auf drei Instanzen einer Anwendung durch. Hierzu erstellen Sie eine JSON-Datei, indem Sie den folgenden JSON-Text verwenden und an einem zugänglichen Speicherort speichern.

```json
{ "instances": 3 }
```

Führen Sie den folgenden Befehl aus, um die Anwendung horizontal hochzuskalieren:

> [!NOTE]
> Der URI lautet „http:\//localhost/marathon/v2/apps/“, gefolgt von der ID der zu skalierenden Anwendung. Wenn Sie das hier bereitgestellte Nginx-Beispiel verwenden, lautet der URI „http:\//localhost/marathon/v2/apps/nginx“.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Nächste Schritte
* Informieren Sie sich auf der Apache Mesos-Website ausführlicher über [HTTP-Endpunkte](https://mesos.apache.org/documentation/latest/endpoints/).
* Weitere Informationen zur [Marathon-REST-API](https://mesosphere.github.io/marathon/docs/rest-api.html)

