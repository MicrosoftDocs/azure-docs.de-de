---
title: (VERALTET) Überwachen eines Azure-DC/OS-Clusters – ELK-Stapel
description: Überwachen eines DC/OS-Clusters in Azure Container Service-Clustern mit ELK (Elasticsearch, Logstash und Kibana).
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 342cf23db2df7d7c79a2b56df96d1a78d6ba215e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61467713"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-elk"></a>(VERALTET) Überwachen eines Azure Container Service-Clusters mit ELK

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

In diesem Artikel wird erläutert, wie Sie den ELK-Stapel (Elasticsearch, Logstash, Kibana) in einem DC/OS-Cluster in Azure Container Service bereitstellen. 

## <a name="prerequisites"></a>Voraussetzungen
Sie müssen einen per Azure Container Service konfigurierten DC/OS-Cluster [bereitstellen](container-service-deployment.md) und eine [Verbindung damit herstellen](../container-service-connect.md). [Hier](container-service-mesos-marathon-ui.md) finden Sie weitere Informationen zum DC/OS-Dashboard und zu den Marathon-Diensten. Installieren Sie auch den [Marathon-Lastenausgleich](container-service-load-balancing.md).


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Elasticsearch, Logstash, Kibana)
Beim ELK-Stapel handelt es sich um eine Kombination aus Elasticsearch, Logstash und Kibana für einen End-to-End-Stapel, der zum Überwachen und Analysieren von Protokollen in Ihrem Cluster verwendet werden kann.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Konfigurieren des ELK-Stapels in einem DC/OS-Cluster
Greifen Sie über [http://localhost:80/](http://localhost:80/) auf die DC/OS-Benutzeroberfläche zu. Navigieren Sie anschließend zu **Universe**. Suchen und installieren Sie Elasticsearch, Logstash und Kibana in DC/OS Universe in genau dieser Reihenfolge. Weitere Informationen zur Konfiguration finden Sie unter dem Link **Advanced Installation** (Erweiterte Installation).

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

Sobald die ELK-Container ausgeführt werden, müssen Sie festlegen, dass auf Kibana über Marathon-LB zugegriffen werden kann. Navigieren Sie wie unten gezeigt zu **Services** > **kibana** (Dienste > kibana), und klicken Sie auf **Edit** (Bearbeiten).

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


Wechseln Sie in den **JSON-Modus**, und scrollen Sie nach unten zum Abschnitt mit den Bezeichnungen.
Sie müssen wie unten gezeigt den Eintrag `"HAPROXY_GROUP": "external"` hinzufügen.
Nachdem Sie auf **Deploy changes** (Änderungen bereitstellen) geklickt haben, wird der Container neu gestartet.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Wenn Sie überprüfen möchten, ob Kibana als Dienst auf dem HAPROXY-Dashboard ausgeführt wird, müssen Sie Port 9090 für den Agent-Cluster öffnen, da HAPROXY an Port 9090 ausgeführt wird.
Standardmäßig werden die Ports 80, 8080 und 443 im DC/OS-Agent-Cluster geöffnet.
Anweisungen zum Öffnen eines Ports und zum Ermöglichen des öffentlichen Zugriffs finden Sie [hier](container-service-enable-public-access.md).

Öffnen Sie zum Zugreifen auf das HAPROXY-Dashboard die Marathon-LB-Administratorschnittstelle unter `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`.
Wenn Sie diese URL aufrufen, sollten das HAPROXY-Dashboard wie unten dargestellt und ein Diensteintrag für Kibana angezeigt werden.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


Zum Zugreifen auf das Kibana-Dashboard (bereitgestellt an Port 5601) müssen Sie Port 5601 öffnen. Befolgen Sie die [hier](container-service-enable-public-access.md) aufgeführten Anweisungen. Öffnen Sie anschließend das Kibana-Dashboard unter `http://localhost:5601`.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur Weiterleitung und Einrichtung von System- und Anwendungsprotokollen finden Sie unter [Protokollverwaltung in DC/OS mit ELK](https://docs.mesosphere.com/1.8/administration/logging/elk/).

* Weitere Informationen zum Filtern von Protokollen finden Sie unter [Filtern von Protokollen mit ELK](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/). 

 

