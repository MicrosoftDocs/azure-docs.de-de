---
title: (VERALTET) Überwachen von Azure-Kubernetes-Clustern – Operations Management
description: Überwachen eines Kubernetes-Clusters in Azure Container Service mithilfe von Log Analytics
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: d7370fc14a5ede23744e04ac9d35140f2368e21f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60711785"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-log-analytics"></a>(VERALTET) Überwachen eines Azure Container Service-Clusters mit Log Analytics

> [!TIP]
> Die aktualisierte Version dieses Artikels zu Azure Kubernetes Service finden Sie unter [Azure Monitor für Container](../../azure-monitor/insights/container-insights-overview.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Voraussetzungen
In dieser exemplarischen Vorgehensweise wird davon ausgegangen, dass Sie mit [Azure Container Service einen Kubernetes-Cluster erstellt haben](container-service-kubernetes-walkthrough.md).

Außerdem wird angenommen, dass die Tools `az` (Azure-Befehlszeilenschnittstelle) und `kubectl` installiert sind.

Führen Sie Folgendes aus, um zu prüfen, ob das Tool `az` installiert ist:

```console
$ az --version
```

Wenn das Tool `az` nicht installiert ist, finden Sie [hier](https://github.com/azure/azure-cli#installation) Anweisungen.
Alternativ können Sie [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) verwenden. In diesen Dienst sind die Azure-Befehlszeilenschnittstelle `az` und die `kubectl`-Tools bereits installiert.

Führen Sie Folgendes aus, um zu prüfen, ob das Tool `kubectl` installiert ist:

```console
$ kubectl version
```

Wenn `kubectl` nicht installiert ist, können Sie folgenden Befehl ausführen:
```console
$ az acs kubernetes install-cli
```

Um zu testen, ob im kubectl-Tool Kubernetes-Schlüssel installiert sind, können Sie folgenden Befehl ausführen:
```console
$ kubectl get nodes
```

Wenn mit diesem Befehl Fehler auftreten, müssen Sie Kubernetes-Clusterschlüssel im kubectl-Tool installieren. Dies ist mit dem folgenden Befehl möglich:
```console
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>Überwachen von Containern mit Log Analytics

Log Analytics ist die cloudbasierte IT-Verwaltungslösung von Microsoft, die Ihnen das Verwalten und Schützen Ihrer lokalen und cloudbasierten Infrastruktur erleichtert. Die Containerlösung ist eine Lösung in Log Analytics, mit der Sie das Inventar, die Leistung und die Protokolle von Containern an einer zentralen Stelle anzeigen können. Sie können Container überwachen und Problembehebungen durchführen, indem Sie die Protokolle an einer zentralen Stelle anzeigen und Container mit zu hoher Aktivität und zu hohem Ressourcenverbrauch auf einem Host suchen.

![](media/container-service-monitoring-oms/image1.png)

Weitere Informationen zur Containerlösung finden Sie unter [Log Analytics-Containerlösung](../../azure-monitor/insights/containers.md).

## <a name="installing-log-analytics-on-kubernetes"></a>Installieren von Log Analytics auf Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Abrufen von Arbeitsbereichs-ID und -Schlüssel
Damit der Log Analytics-Agent mit dem Dienst kommunizieren kann, muss er mit einer Arbeitsbereichs-ID und einem Arbeitsbereichsschlüssel konfiguriert werden. Um die Arbeitsbereichs-ID und den Schlüssel zu erhalten, müssen Sie unter <https://mms.microsoft.com> ein Konto erstellen.
Führen Sie die entsprechenden Schritte aus, um ein Konto zu erstellen. Ist die Erstellung des Kontos abgeschlossen, können Sie Ihre ID und den Schlüssel abrufen, indem Sie auf das Blatt **Log Analytics** und dann auf den Namen Ihres Arbeitsbereichs klicken. Wählen Sie dann wie nachfolgend gezeigt **Erweiterte Einstellungen**, **Verbundene Quellen** und dann **Linux-Server**, um die erforderlichen Informationen anzuzeigen.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-log-analytics-agent-using-a-daemonset"></a>Installieren des Log Analytics-Agents mithilfe eines DaemonSet
DaemonSets werden von Kubernetes verwendet, um auf jedem Host im Cluster eine einzelne Instanz eines Containers auszuführen.
Sie eignen sich perfekt zum Ausführen von Überwachungs-Agents.

Im Anschluss sehen Sie die [DaemonSet-YAML-Datei](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Speichern Sie sie in einer Datei namens `oms-daemonset.yaml`, und ersetzen Sie die Platzhalterwerte für `WSID` und `KEY` durch Ihre Arbeitsbereichs-ID und den dazugehörigen Schlüssel.

Nachdem Sie der DaemonSet-Konfiguration Ihre Arbeitsbereichs-ID und den Schlüssel hinzugefügt haben, können Sie den Log Analytics-Agent mithilfe des `kubectl`-Befehlszeilentools in Ihrem Cluster installieren:

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-log-analytics-agent-using-a-kubernetes-secret"></a>Installieren des Log Analytics-Agents mit einem Kubernetes-Geheimnis
Zum Schutz der Log Analytics-Arbeitsbereichs-ID und des Schlüssels können Sie ein Kubernetes-Geheimnis als Teil der DaemonSet-YAML-Datei verwenden.

- Kopieren Sie das Skript, die Vorlagendatei für Geheimnisse und die DaemonSet-YAML-Datei (aus dem [Repository](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)), und vergewissern Sie sich, dass sich alle im selben Verzeichnis befinden.
  - Skript zum Generieren von Geheimnissen – „secret-gen.sh“
  - Vorlage für Geheimnisse – „secret-template.yaml“
    - DaemonSet-YAML-Datei – „omsagent-ds-secrets.yaml“
- Führen Sie das Skript aus. Im Skript erfolgt eine Aufforderung zur Eingabe der Log Analytics-Arbeitsbereichs-ID und des Primärschlüssels. Geben Sie diese Werte ein. Danach erstellt das Skript eine YAML-Datei für Geheimnisse, sodass Sie sie ausführen können.
  ```
  #> sudo bash ./secret-gen.sh
  ```

  - Erstellen Sie den Pod für Geheimnisse durch Ausführen des folgenden Befehls: ```kubectl create -f omsagentsecret.yaml```

  - Führen Sie zur Überprüfung Folgendes aus:

  ```
  root@ubuntu16-13db:~# kubectl get secrets
  NAME                  TYPE                                  DATA      AGE
  default-token-gvl91   kubernetes.io/service-account-token   3         50d
  omsagent-secret       Opaque                                2         1d
  root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
  Name:           omsagent-secret
  Namespace:      default
  Labels:         <none>
  Annotations:    <none>

  Type:   Opaque

  Data
  ====
  WSID:   36 bytes
  KEY:    88 bytes
  ```

  - Erstellen Sie Ihr OMS-Agent-DaemonSet durch Ausführen des folgenden Befehls: ```kubectl create -f omsagent-ds-secrets.yaml```

### <a name="conclusion"></a>Zusammenfassung
Das ist alles! Nach wenigen Minuten sollten Daten bei Ihrem Log Analytics-Dashboard eingehen.
