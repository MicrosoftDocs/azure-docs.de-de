---
title: (VERALTET) Überwachen von Azure-Kubernetes-Clustern mit CoScale
description: Überwachen eines Kubernetes-Clusters in Azure Container Service mithilfe von CoScale
services: container-service
author: fryckbos
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 895346447e33926dcaa5ca09302f35c9d6636ed9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60713040"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>(VERALTET) Überwachen eines Azure Container Service-Kubernetes-Clusters mit CoScale

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

In diesem Artikel erfahren Sie, wie Sie den [CoScale](https://web.archive.org/web/20180317071550/ https://www.coscale.com/)-Agent zum Überwachen aller Knoten und Container in Ihrem Kubernetes-Cluster in Azure Container Service bereitstellen. Für diese Konfiguration benötigen Sie ein Konto mit CoScale. 


## <a name="about-coscale"></a>CoScale 

CoScale ist eine Überwachungsplattform, über die Metriken und Ereignisse von allen Containern in mehreren Orchestrierungsplattformen gesammelt werden. CoScale bietet eine umfassende Überwachung für Kubernetes-Umgebungen. CoScale stellt Visualisierungen und Analysen für alle Ebenen im Stack bereit: Betriebssystem, Kubernetes, Docker und Anwendungen, die in Ihren Containern ausgeführt werden. CoScale umfasst mehrere integrierte Überwachungsdashboards und verfügt über eine integrierte Anomalieerkennung, sodass Operatoren und Entwickler Infrastruktur- und Anwendungsprobleme schnell finden können.

![CoScale-Benutzeroberfläche](./media/container-service-kubernetes-coscale/coscale.png)

Wie in diesem Artikel erläutert wird, können Sie Agents in einem Kubernetes-Cluster installieren, um CoScale als SaaS-Lösung auszuführen. Wenn Sie Ihre Daten lokal beibehalten möchten, kann CoScale auch lokal installiert werden.


## <a name="prerequisites"></a>Voraussetzungen

Sie müssen zunächst [ein CoScale-Konto erstellen](https://web.archive.org/web/20170507123133/ https://www.coscale.com/free-trial).

In dieser exemplarischen Vorgehensweise wird davon ausgegangen, dass Sie mit [Azure Container Service einen Kubernetes-Cluster erstellt haben](container-service-kubernetes-walkthrough.md).

Außerdem wird vorausgesetzt, dass die Azure-Befehlszeilenschnittstelle `az` und `kubectl`-Tools installiert sind.

Führen Sie Folgendes aus, um zu prüfen, ob das Tool `az` installiert ist:

```azurecli
az --version
```

Wenn das Tool `az` nicht installiert ist, finden Sie [hier](/cli/azure/install-azure-cli) Anweisungen.

Führen Sie Folgendes aus, um zu prüfen, ob das Tool `kubectl` installiert ist:

```bash
kubectl version
```

Wenn `kubectl` nicht installiert ist, können Sie folgenden Befehl ausführen:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>Installieren des CoScale-Agents mit einem DaemonSet
[DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) werden von Kubernetes verwendet, um auf jedem Host im Cluster eine einzelne Instanz eines Containers auszuführen.
Sie eignen sich perfekt zum Ausführen von Überwachungs-Agents, z.B. des CoScale-Agents.

Nachdem Sie sich bei CoScale angemeldet haben, navigieren Sie zu der [agent page](https://app.coscale.com/) (Seite für Agents), um mithilfe eines DaemonSet CoScale-Agents in Ihrem Cluster zu installieren. Die CoScale-Benutzeroberfläche bietet geführte Konfigurationsschritte zum Erstellen eines Agents und Starten der Überwachung Ihres gesamten Kubernetes-Clusters.

![Konfiguration eines CoScale-Agents](./media/container-service-kubernetes-coscale/installation.png)

Um den Agent im Cluster zu starten, führen Sie den angegebenen Befehl aus:

![Starten des CoScale-Agents](./media/container-service-kubernetes-coscale/agent_script.png)

Das ist alles! Wenn die Agents ausgeführt werden, sollten innerhalb weniger Minuten Daten an der Konsole angezeigt werden. Besuchen Sie die [agent page](https://app.coscale.com/) (Seite für Agents), um eine Zusammenfassung Ihres Clusters anzuzeigen, weitere Konfigurationsschritte auszuführen und Dashboards wie z.B. **Kubernetes Cluster Overview** (Übersicht über Kubernetes-Cluster) anzuzeigen.

![Übersicht über Kubernetes-Cluster](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

Der CoScale-Agent wird automatisch auf neuen Computern im Cluster bereitgestellt. Der Agent wird automatisch aktualisiert, wenn eine neue Version veröffentlicht wird.


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu CoScale-Überwachungslösungen finden Sie in der [Dokumentation zu CoScale](https://web.archive.org/web/20180415164304/ http://docs.coscale.com:80/) und in diesem [Blog](https://web.archive.org/web/20170501021344/ http://www.coscale.com:80/blog). 

