---
title: (VERALTET) Lastenausgleich für Container in Azure-DC/OS-Clustern
description: Es wird beschrieben, wie Sie den Lastenausgleich für mehrere Container in einem Azure Container Service-DC/OS-Cluster durchführen.
services: container-service
author: rgardler
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 06/02/2017
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 1e4c978a8767154fb6a1f9a822cb0dd8d1b8796e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66148888"
---
# <a name="deprecated-load-balance-containers-in-an-azure-container-service-dcos-cluster"></a>(VERALTET) Vornehmen eines Lastausgleichs für Container in einem Azure Container Service-DC/OS-Cluster

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

In diesem Artikel wird beschrieben, wie Sie einen internen Lastenausgleich in einem per DC/OS verwalteten Azure Container Service mit Marathon-LB erstellen. Durch diese Konfiguration wird das horizontale Skalieren Ihrer Anwendungen ermöglicht. Außerdem können Sie die öffentlichen und privaten Agent-Cluster nutzen, indem Sie Ihre Load Balancer im öffentlichen Cluster und Ihre Anwendungscontainer im privaten Cluster anordnen. In diesem Tutorial haben Sie Folgendes durchgeführt:

> [!div class="checklist"]
> * Konfigurieren eines Marathon Load Balancers
> * Bereitstellen einer Anwendung mithilfe des Load Balancers
> * Konfigurieren eines Azure Load Balancers

Sie benötigen einen ACS DC/OS-Cluster, um die Schritte in diesem Tutorial auszuführen. Bei Bedarf können Sie mit diesem [Beispielskript](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) einen erstellen.

Für dieses Tutorial ist mindestens Version 2.0.4 der Azure CLI erforderlich. Führen Sie `az --version` aus, um die Version zu finden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle]( /cli/azure/install-azure-cli) weitere Informationen. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="load-balancing-overview"></a>Übersicht über den Lastenausgleich

In einem Azure Container Service DC/OS-Cluster gibt es zwei Ebenen für den Lastenausgleich: 

**Azure Load Balancer** bietet öffentliche Einstiegspunkte (auf die Endbenutzer zugreifen). Ein Azure Load Balancer wird vom Azure Container Service automatisch bereitgestellt und standardmäßig so konfiguriert, dass die Ports 80, 443 und 8080 verfügbar gemacht werden.

Der **Marathon Load Balancer (marathon-lb)** leitet eingehende Anforderungen an Containerinstanzen weiter, die diese Anforderungen verarbeiten. Wenn wir die Container für unseren Webdienst skalieren, wird marathon-lb dynamisch angepasst. Dieser Load Balancer wird nicht standardmäßig in Ihrem Containerdienst bereitgestellt, ist aber sehr einfach zu installieren.

## <a name="configure-marathon-load-balancer"></a>Konfigurieren eines Marathon Load Balancers

Marathon Load Balancer konfiguriert sich basierend auf den von Ihnen bereitgestellten Containern selbst dynamisch neu. Außerdem kann er den Verlust eines Containers oder Agents überstehen. Wenn dies passiert, wird der Container von Apache Mesos an einem anderen Ort neu gestartet, an den sich marathon-lb anpasst.

Führen Sie den folgenden Befehl aus, um den Marathon Load Balancer für den Cluster des öffentlichen Agents zu installieren.

```azurecli-interactive
dcos package install marathon-lb
```

## <a name="deploy-load-balanced-application"></a>Bereitstellen einer Anwendung mit Lastenausgleich

Da wir jetzt über das „marathon-lb“-Paket verfügen, können wir einen Anwendungscontainer bereitstellen, für den der Lastenausgleich durchgeführt werden soll. 

Rufen Sie zunächst den FQDN der öffentlich verfügbar gemachten Agents ab.

```azurecli-interactive
az acs list --resource-group myResourceGroup --query "[0].agentPoolProfiles[0].fqdn" --output tsv
```

Als Nächstes erstellen Sie eine Datei namens *hello-web.json*, in die Sie den folgenden Inhalt kopieren. Die Bezeichnung `HAPROXY_0_VHOST` muss mit dem FQDN des DC/OS-Agents aktualisiert werden. 

```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "yeasy/simple-web",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  }
}
```

Verwenden Sie die CLI von DC/OS, um die Anwendung auszuführen. Standardmäßig stellt Marathon die Anwendung im privaten Cluster bereit. Dies bedeutet, dass auf die oben genannte Bereitstellung nur über Ihren Load Balancer zugegriffen werden kann, was in der Regel das gewünschte Verhalten ist.

```azurecli-interactive
dcos marathon app add hello-web.json
```

Nachdem die Anwendung bereitgestellt wurde, navigieren Sie zum FQDN des Agent-Clusters, um die Anwendung mit Lastenausgleich anzuzeigen.

![Abbildung einer Anwendung mit Lastenausgleich](./media/container-service-load-balancing/lb-app.png)

## <a name="configure-azure-load-balancer"></a>Konfigurieren von Azure Load Balancer

Azure Load Balancer macht standardmäßig die Ports 80, 8080 und 443 verfügbar. Bei Verwendung von einem dieser drei Ports (wie im obigen Beispiel) müssen Sie nichts weiter unternehmen. Der FQDN des Load Balancers Ihres Agents sollte auf Anhieb gefunden werden. Bei jeder von Ihnen durchgeführten Aktualisierung wird rollierend jeweils ein anderer Ihrer drei Webserver gefunden. 

Wenn Sie einen anderen Port verwenden, müssen Sie eine Roundrobinregel und für den jeweiligen Port einen Test für den Load Balancer hinzufügen. Hierfür können Sie die [Azure-Befehlszeilenschnittstelle](../../azure-resource-manager/xplat-cli-azure-resource-manager.md) mit den Befehlen `azure network lb rule create` und `azure network lb probe create` verwenden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie mehr über den Lastenausgleich in ACS mit sowohl dem Marathon als auch dem Azure Load Balancer sowie über die folgenden Aktionen erfahren:

> [!div class="checklist"]
> * Konfigurieren eines Marathon Load Balancers
> * Bereitstellen einer Anwendung mithilfe des Load Balancers
> * Konfigurieren eines Azure Load Balancers

Fahren Sie mit dem nächsten Tutorial fort, um mehr über die Integration von Azure-Speicher und DC/OS in Azure zu erfahren.

> [!div class="nextstepaction"]
> [Bereitstellen einer Azure-Dateifreigabe in einem DC/OS-Cluster](container-service-dcos-fileshare.md)