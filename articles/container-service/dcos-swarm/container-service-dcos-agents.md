---
title: (VERALTET) DC/OS-Agent-Pools für Azure Container Service
description: Funktionsweise öffentlicher und privater Agent-Pools mit einem Azure Container Service-DC/OS-Cluster
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 01/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 03cacda1aa405cb2d0ded579c8ddb5f6011ce3bb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60478445"
---
# <a name="deprecated-dcos-agent-pools-for-azure-container-service"></a>(VERALTET) DC/OS-Agent-Pools für Azure Container Service

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS-Cluster in Azure Container Service enthalten die Agent-Knoten in zwei Pools, einem öffentlichen und einem privaten Pool. Die Bereitstellung einer Anwendung ist in beiden Pools möglich, der Pool hat jedoch Auswirkungen auf den Zugriff zwischen Computern in Ihrem Containerdienst. Die Computer können über Internetzugriff (öffentlich) verfügen oder nur intern (privat) verwendet werden. Dieser Artikel geht kurz darauf ein, warum ein öffentlicher und ein privater Pool zur Verfügung stehen.


* **Private Agents**: Private Agentknoten werden über ein nicht routingfähiges Netzwerk ausgeführt. Über dieses Netzwerk kann nur von der Administratorzone oder über den Edgerouter der öffentlichen Zone zugegriffen werden. Standardmäßig startet DC/OS Apps auf privaten Agentknoten. 

* **Öffentliche Agents**: Öffentliche Agent-Knoten führen DC/OS-Apps und -Dienste über ein öffentlich zugängliches Netzwerk aus. 

Weitere Informationen zur DC/OS-Netzwerksicherheit finden Sie in der [DC/OS-Dokumentation](https://docs.mesosphere.com/).

## <a name="deploy-agent-pools"></a>Bereitstellen von Agent-Pools

DC/OS-Agent-Pools werden in Azure Container Service folgendermaßen erstellt:

* Der **private Pool** enthält die Anzahl der Agent-Knoten, die Sie angeben, wenn Sie [DC/OS-Cluster bereitstellen](container-service-deployment.md). 

* Der **öffentliche Pool** enthält eine zuvor festgelegte Anzahl von Agent-Knoten. Dieser Pool wird automatisch hinzugefügt, wenn der DC/OS-Cluster bereitgestellt wird.

Der private Pool und der öffentliche Pool sind Azure-VM-Skalierungsgruppen. Sie können die Größe dieser Pools nach der Bereitstellung ändern.

## <a name="use-agent-pools"></a>Verwenden von Agent-Pools
Standardmäßig stellt **Marathon** jede neue Anwendung auf den *privaten* Agentknoten bereit. Sie müssen die Anwendung bei der Anwendungserstellung explizit auf dem *öffentlichen* Knoten bereitstellen. Geben Sie dazu auf der Registerkarte **Optional** für den Wert **Accepted Resource Roles** den Text **slave_public** ein. Dieser Prozess ist [hier](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) und in der [DC/OS](https://docs.mesosphere.com/1.7/administration/installing/oss/custom/create-public-agent/)-Dokumentation beschrieben.

## <a name="next-steps"></a>Nächste Schritte
* Erhalten Sie weitere Informationen zum [Verwalten Ihrer DC/OS-Container](container-service-mesos-marathon-ui.md).

* Erfahren Sie, wie Sie die von Azure bereitgestellte [Firewall öffnen](container-service-enable-public-access.md), um den Zugriff auf Ihre DC/OS-Container zu ermöglichen.

