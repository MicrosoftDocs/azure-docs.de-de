---
title: (VERALTET) Überwachen von Azure Kubernetes-Clustern – Sysdig
description: Überwachen eines Kubernetes-Clusters in Azure Container Service mithilfe von Sysdig
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 4aef241e2c86e4016c3c468fcdcfdfc620fc7aa9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60309260"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>(VERALTET) Überwachen eines Azure Container Service-Kubernetes-Clusters mithilfe von Sysdig

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Voraussetzungen
In dieser exemplarischen Vorgehensweise wird davon ausgegangen, dass Sie mit [Azure Container Service einen Kubernetes-Cluster erstellt haben](container-service-kubernetes-walkthrough.md).

Ferner wird angenommen, dass die Azure CLI und Kubectl-Tools installiert sind.

Führen Sie Folgendes aus, um zu prüfen, ob das Tool `az` installiert ist:

```console
$ az --version
```

Wenn das Tool `az` nicht installiert ist, finden Sie [hier](https://github.com/azure/azure-cli#installation) Anweisungen.

Führen Sie Folgendes aus, um zu prüfen, ob das Tool `kubectl` installiert ist:

```console
$ kubectl version
```

Wenn `kubectl` nicht installiert ist, können Sie folgenden Befehl ausführen:

```console
$ az acs kubernetes install-cli
```

## <a name="sysdig"></a>Sysdig
Sysdig ist ein externer Monitoring-as-a-Service-Anbieter, der Container in Ihrem Kubernetes-Cluster unter Azure überwachen kann. Für die Nutzung von Sysdig wird ein aktives Sysdig-Konto benötigt.
Auf [deren Website](https://app.sysdigcloud.com) können Sie sich für ein Konto registrieren.

Klicken Sie nach dem Anmelden an der Sysdig-Cloudwebsite auf Ihren Benutzernamen. Auf der Seite sollte Ihr „Zugriffsschlüssel“ angezeigt werden. 

![Sysdig-API-Schlüssel](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>Installieren der Sysdig-Agents für Kubernetes
Zur Überwachung Ihrer Container führt Sysdig auf jedem Computer einen Prozess mit einem Kubernetes-`DaemonSet` aus.
DaemonSets sind Kubernetes-API-Objekte, die pro Computer eine einzelne Instanz eines Containers ausführen.
Sie eignen sich perfekt für die Installation von Tools wie den Sysdig-Überwachungs-Agent.

Um das Sysdig-DaemonSet zu installieren, müssen Sie zunächst die [Vorlage](https://github.com/draios/sysdig-cloud-scripts/tree/master/agent_deploy/kubernetes) von Sysdig herunterladen. Speichern Sie die Datei als `sysdig-daemonset.yaml`.

Unter Linux und OS X können Sie Folgendes ausführen:

```console
$ curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

PowerShell:

```console
$ Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

Fügen Sie als Nächstes Ihren Zugriffsschlüssel, den Sie aus Ihrem Sysdig-Konto abgerufen haben, in die Datei ein.

Erstellen Sie abschließend das DaemonSet:

```console
$ kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>Anzeigen Ihrer Überwachung
Sobald die Agents nach der Installation ausgeführt werden, übermitteln sie Daten an Sysdig.  Kehren Sie zum [Sysdig-Dashboard](https://app.sysdigcloud.com) zurück, und sehen Sie sich die Informationen zu Ihren Containern an.

Mithilfe des [Dashboarderstellungs-Assistenten](https://app.sysdigcloud.com/#/dashboards/new) können Sie auch Kubernetes-spezifische Dashboards installieren.
