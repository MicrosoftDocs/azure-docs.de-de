---
title: Platzieren einer PostgreSQL Hyperscale-Servergruppe auf Kubernetes-Clusterknoten
description: Hier wird erläutert, wie PostgreSQL-Instanzen, die eine PostgreSQL Hyperscale-Servergruppe bilden, auf den Kubernetes-Clusterknoten platziert werden.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ecc2e98d4c6c58e11b2bdc86b623f31d828cabc0
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98985919"
---
# <a name="azure-arc-enabled-postgresql-hyperscale-server-group-placement"></a>Platzieren einer Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe

In diesem Artikel wird anhand eines Beispiels veranschaulicht, wie die PostgreSQL-Instanzen einer Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe auf den physischen Knoten des Kubernetes-Clusters platziert werden, auf dem sie gehostet wird. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configuration"></a>Konfiguration

In diesem Beispiel verwenden wir einen AKS-Cluster (Azure Kubernetes Service) mit vier physischen Knoten. 

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/1_cluster_portal.png" alt-text="AKS-Cluster mit vier Knoten im Azure-Portal":::

Führen Sie den folgenden Befehl aus, um die physischen Knoten des Kubernetes-Clusters aufzulisten:

```console
kubectl get nodes
```

Dadurch werden die vier physischen Knoten im Kubernetes-Cluster angezeigt:

```output
NAME                                STATUS   ROLES   AGE   VERSION
aks-agentpool-42715708-vmss000000   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000001   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000002   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000003   Ready    agent   11h   v1.17.9
```

Die Architektur kann wie folgt dargestellt werden:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/2_logical_cluster.png" alt-text="Logische Darstellung von vier Knoten, die in einem Kubernetes-Cluster gruppiert sind":::

Auf dem Kubernetes-Cluster werden ein Azure Arc-Datencontroller und eine Azure Arc-fähige PostgreSQL Hyperscale-Servergruppe gehostet. Diese Servergruppe besteht aus drei PostgreSQL-Instanzen: einem Koordinator und zwei Workern.

Listen Sie die Pods mit dem folgenden Befehl auf:

```console
kubectl get pods -n arc3
```
Die folgende Ausgabe wird erzeugt:

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01c-0         3/3     Running   0          9h
postgres01w-0         3/3     Running   0          9h
postgres01w-1         3/3     Running   0          9h
```
Auf jedem dieser Pods wird eine PostgreSQL-Instanz gehostet. Diese bilden zusammen die Azure Arc-fähige PostgreSQL Hyperscale-Servergruppe:

```output
Pod name        Role in the server group
postgres01c-0 Coordinator
postgres01w-0   Worker
postgres01w-1   Worker
```

## <a name="placement"></a>Platzierung
Sehen wir uns nun an, wie Kubernetes die Pods der Servergruppe platziert. Beschreiben Sie die einzelnen Pods, und legen Sie fest, auf welchem physischen Knoten des Kubernetes-Clusters sie platziert werden. Führen Sie für den Koordinator z. B. den folgenden Befehl aus:

```console
kubectl describe pod postgres01c-0 -n arc3
```

Die folgende Ausgabe wird erzeugt:

```output
Name:         postgres01c-0
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
Start Time:   Thu, 17 Sep 2020 00:40:33 -0700
…
```

Wenn wir diesen Befehl für die einzelnen Pods ausführen, fassen wir die aktuelle Platzierung wie folgt zusammen:

| Servergruppenrolle|Servergruppenpod|Physischer Kubernetes-Knoten, auf dem der Pod gehostet wird |
|--|--|--|
| Worker|postgres01-1|aks-agentpool-42715708-vmss000002 |
| Worker|postgres01-2|aks-agentpool-42715708-vmss000003 |

Notieren Sie in der Beschreibung der Pods auch die Namen der Container, die von den einzelnen Pods gehostet werden. Führen Sie für den zweiten Worker z. B. den folgenden Befehl aus:

```console
kubectl describe pod postgres01w-1 -n arc3
```

Die folgende Ausgabe wird erzeugt:

```output
…
Node:         aks-agentpool-42715708-vmss000003/10.240.0.7
..
Containers:
  Fluentbit:
…
  Postgres:
…
  Telegraf:
…
```

Jeder Pod, der Teil der Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe ist, hostet die folgenden drei Container:

|Container|Beschreibung
|----|----|
|`Fluentbit` |Data *-Protokollsammler: https://fluentbit.io/
|`Postgres`|Teil der PostgreSQL-Instanz in der Azure Arc-fähigen PosgreSQL Hyperscale-Servergruppe
|`Telegraf` |Metrikensammler: https://www.influxdata.com/time-series-platform/telegraf/

Die Architektur sieht wie folgt aus:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/3_pod_placement.png" alt-text="Drei Pods, die jeweils auf separaten Knoten platziert sind":::

Dies bedeutet, dass an diesem Punkt jede PostgreSQL-Instanz, die Bestandteil der Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe ist, auf einem bestimmten physischen Host im Kubernetes-Container gehostet wird. Diese Konfiguration ist ideal, um die optimale Leistung der Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe zu erzielen, da die einzelnen Rollen (Koordinator und Worker) die Ressourcen der einzelnen physischen Knoten nutzen. Diese Ressourcen werden nicht von mehreren PostgreSQL-Rollen gemeinsam genutzt.

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale"></a>Aufskalieren von Azure Arc-fähigem PostgreSQL Hyperscale

Nun wollen wir die Servergruppe mit einem dritten Workerknoten aufskalieren und beobachten, was passiert. Hiermit wird eine vierte PostgreSQL-Instanz erstellt, die auf einem vierten Pod gehostet wird.
Zum Aufskalieren führen Sie den folgenden Befehl aus:

```console
azdata arc postgres server edit --name postgres01 --workers 3
```

Hierdurch wird folgende Ausgabe generiert:

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

Listen Sie die im Azure Arc-Datencontroller bereitgestellten Servergruppen auf, und überprüfen Sie, ob die Servergruppe nun mit drei Workern ausgeführt wird. Führen Sie den folgenden Befehl aus:

```console
azdata arc postgres server list
```

Wie Sie sehen, ist eine Aufskalierung von zwei auf drei Worker erfolgt:

```output
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

Wie zuvor können wir erkennen, dass die Servergruppe nun insgesamt vier Pods verwendet:

```console
kubectl get pods -n arc3
```

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01c-0         3/3     Running   0          11h
postgres01w-0         3/3     Running   0          11h
postgres01w-1         3/3     Running   0          11h
postgres01w-2         3/3     Running   0          5m2s
```

Beschreiben Sie den neuen Pod, um anzugeben, auf welchem der physischen Knoten des Kubernetes-Clusters er gehostet wird.
Führen Sie den folgenden Befehl aus:

```console
kubectl describe pod postgres01w-2 -n arc3
```

So identifizieren Sie den Namen des Hostknotens

```output
Name:         postgres01w-2
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
```

Die Platzierung der PostgreSQL-Instanzen auf den physischen Knoten des Clusters sieht jetzt wie folgt aus:

|Servergruppenrolle|Servergruppenpod|Physischer Kubernetes-Knoten, auf dem der Pod gehostet wird
|-----|-----|-----
|Koordinator|postgres01-0|aks-agentpool-42715708-vmss000000
|Worker|postgres01-1|aks-agentpool-42715708-vmss000002
|Worker|postgres01-2|aks-agentpool-42715708-vmss000003
|Worker|postgres01-3|aks-agentpool-42715708-vmss000000

Beachten Sie, dass der Pod des neuen Workers (postgres01w-2) auf demselben Knoten wie der Koordinator platziert wurde. 

Die Architektur sieht wie folgt aus:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/4_pod_placement_.png" alt-text="Vierter Pod auf demselben Knoten wie der Koordinator":::

Warum befindet sich der neue Worker/Pod nicht auf dem verbleibenden physischen Knoten des Kubernetes-Clusters aks-agentpool-42715708-vmss000003?

Der Grund hierfür ist, dass der letzte physische Knoten des Kubernetes-Clusters tatsächlich mehrere Pods und in diesen zusätzliche Komponenten hostet, die zum Ausführen von Azure Arc-fähigen Datendiensten erforderlich sind. Kubernetes hat ermittelt, dass – zum Zeitpunkt der Planung – der beste Kandidat zum Hosten des zusätzlichen Workers der physische Knoten aks-agentpool-42715708-vmss000000 ist. 

Sehen wir uns mit den gleichen Befehlen wie an, was auf den einzelnen physischen Knoten gehostet wird:

|Namen anderer Pods\* |Verwendung|Physischer Kubernetes-Knoten, auf dem die Pods gehostet werden
|----|----|----
|bootstrapper-jh48b|Dies ist ein Dienst, der eingehende Anforderungen zum Erstellen, Bearbeiten und Löschen benutzerdefinierter Ressourcen wie verwaltete SQL-Instanzen, PostgreSQL Hyperscale-Servergruppen und Datencontroller verarbeitet.|aks-agentpool-42715708-vmss000003
|control-gwmbs||aks-agentpool-42715708-vmss000002
|controldb-0|Dies ist der Controllerdatenspeicher, der zum Speichern der Konfiguration und des Zustands für den Datencontroller verwendet wird.|aks-agentpool-42715708-vmss000001
|controlwd-zzjp7|Dies ist der „Watch Dog“-Dienst des Controllers, der die Verfügbarkeit des Datencontrollers überwacht.|aks-agentpool-42715708-vmss000000
|logsdb-0|Dies ist eine Instanz der elastischen Suche, mit der alle Protokolle gespeichert werden, die von allen Arc-Datendienstpods gesammelt werden. Elasticsearch, empfängt Daten aus dem `Fluentbit`-Container der einzelnen Pods|aks-agentpool-42715708-vmss000003
|logsui-5fzv5|Dies ist eine Kibana-Instanz, die auf die elastische Suchdatenbank aufgesetzt ist, um eine grafische Log Analytics-Benutzeroberfläche zu präsentieren.|aks-agentpool-42715708-vmss000003
|metricsdb-0|Dies ist eine InfluxDB-Instanz, die verwendet wird, um alle Metriken zu speichern, die von allen Arc-Datendienstpods gesammelt werden. InfluxDB, empfängt Daten aus dem `Telegraf`-Container der einzelnen Pods|aks-agentpool-42715708-vmss000000
|metricsdc-47d47|Dies ist ein Daemonset, das auf allen Kubernetes-Knoten im Cluster bereitgestellt wird, um Metriken auf Knotenebene von den Knoten zu erfassen.|aks-agentpool-42715708-vmss000002
|metricsdc-864kj|Dies ist ein Daemonset, das auf allen Kubernetes-Knoten im Cluster bereitgestellt wird, um Metriken auf Knotenebene von den Knoten zu erfassen.|aks-agentpool-42715708-vmss000001
|metricsdc-l8jkf|Dies ist ein Daemonset, das auf allen Kubernetes-Knoten im Cluster bereitgestellt wird, um Metriken auf Knotenebene von den Knoten zu erfassen.|aks-agentpool-42715708-vmss000003
|metricsdc-nxm4l|Dies ist ein Daemonset, das auf allen Kubernetes-Knoten im Cluster bereitgestellt wird, um Metriken auf Knotenebene von den Knoten zu erfassen.|aks-agentpool-42715708-vmss000000
|metricsui-4fb7l|Dies ist eine Grafana-Instanz, die auf die InfluxDB-Datenbank aufgesetzt ist, um eine grafische Überwachungsdashboard-Benutzeroberfläche zu präsentieren.|aks-agentpool-42715708-vmss000003
|mgmtproxy-4qppp|Dies ist eine Webanwendungsproxy-Ebene, die sich vor den Grafana- und Kibana-Instanzen befindet.|aks-agentpool-42715708-vmss000002

> \* Das Suffix der Podnamen weicht in anderen Bereitstellungen ab. Außerdem werden hier nur die Pods aufgelistet, die im Kubernetes-Namespace des Azure Arc-Datencontrollers gehostet werden.

Die Architektur sieht wie folgt aus:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/5_full_list_of_pods.png" alt-text="Alle Pods im Namespace auf verschiedenen Knoten":::

Dies bedeutet, dass die Koordinatorknoten (Pod 1) der Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe und der dritte Workerknoten (Pod 4) die gleichen physischen Ressourcen der Servergruppe verwenden. Dies ist akzeptabel, da der Koordinatorknoten in der Regel im Vergleich zu einem Workerknoten sehr wenig Ressourcen nutzt. Daraus folgt, dass Sie beim Konfigurieren folgender Werte sorgfältig vorgehen sollten:
- Größe des Kubernetes-Clusters und die Eigenschaften der einzelnen physischen Knoten (Arbeitsspeicher, virtuelle Kerne)
- Anzahl der physischen Knoten im Kubernetes-Cluster
- Anwendungen oder Workloads, die auf dem Kubernetes-Cluster gehostet werden

Wenn zu viele Workloads auf dem Kubernetes-Cluster gehostet werden, führt dies möglicherweise zu einer Drosselung der Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe. In diesem Fall profitieren Sie nur eingeschränkt von der Möglichkeit einer horizontalen Skalierung. Die Leistung, die Sie mit dem System erzielen, hängt nicht nur von der Platzierung und den physischen Eigenschaften der physischen Knoten und des Speichersystems ab. Die erzielte Leistung richtet sich auch nach der Konfiguration der Ressourcen, die im Kubernetes-Cluster (einschließlich dem Azure Arc-fähigen PostgreSQL Hyperscale) ausgeführt werden, beispielsweise den Anforderungen und den Grenzwerten, die Sie für Arbeitsspeicher und virtuelle Kerne festlegen. Die Workloadmenge, die Sie in einem bestimmten Kubernetes-Cluster hosten können, steht in Relation zu den Eigenschaften des Kubernetes-Clusters, der Art der Workloads, der Anzahl der Benutzer, der Ausführung von Vorgängen im Kubernetes-Cluster usw.

## <a name="scale-out-aks"></a>Aufskalieren des AKS

Im Folgenden wird veranschaulicht, dass die horizontale Skalierung sowohl des AKS-Clusters als auch des Azure Arc-fähigen PostgreSQL Hyperscale-Servers eine Möglichkeit darstellt, um maximal von der hohen Leistung eines Azure Arc-fähigen PostgreSQL Hyperscale zu profitieren.
Fügen Sie dem AKS-Cluster einen fünften Knoten hinzu:

:::row:::
    :::column:::
        Vorher
    :::column-end:::
    :::column:::
        Danach
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/6_layout_before.png" alt-text="Azure-Portal-Layout vorher":::
    :::column-end:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/7_layout_after.png" alt-text="Azure-Portal-Layout nachher":::
    :::column-end:::
:::row-end:::

Die Architektur sieht wie folgt aus:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/8_logical_layout_after.png" alt-text="Logisches Layout auf dem Kubernetes-Cluster nach dem Update":::

Sehen wir uns an, welche Pods des Arc-Datencontroller-Namespace auf dem neuen physischen AKS-Knoten gehostet werden, indem wir den folgenden Befehl ausführen:

```console
kubectl describe node aks-agentpool-42715708-vmss000004
```

Aktualisieren wir nun die Darstellung der Architektur unseres Systems:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/9_updated_list_of_pods.png" alt-text="Alle Pods im logischen Diagramm des Clusters":::

Wir sehen, dass der neue physische Knoten des Kubernetes-Clusters nur den Metrikpod hostet, der für Azure Arc Data Services erforderlich ist. Beachten Sie, dass wir in diesem Beispiel nur den Namespace des Arc-Datencontrollers betrachten, die anderen Pods werden nicht dargestellt.

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale-again"></a>Erneutes Aufskalieren von Azure Arc-fähigem PostgreSQL Hyperscale

Auf dem fünften physischen Knoten wird noch keine Workload gehostet. Beim Aufskalieren des Azure Arc-fähigen PostgreSQL-Hyperscale optimiert Kubernetes die Platzierung des neuen PostgreSQL-Pods, sodass er nicht auf physischen Knoten angeordnet werden sollte, auf denen bereits andere Workloads gehostet werden. Führen Sie den folgenden Befehl aus, um den Azure Arc-fähigen PostgreSQL Hyperscale von drei auf vier Worker zu skalieren. Nach Abschluss dieses Vorgangs besteht die Servergruppe aus fünf PostgreSQL-Instanzen, über die sie verteilt ist: einen Koordinator und vier Worker.

```console
azdata arc postgres server edit --name postgres01 --workers 4
```

Hierdurch wird folgende Ausgabe generiert:

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

Listen Sie die im Datencontroller bereitgestellten Servergruppen auf, und überprüfen Sie, ob die Servergruppe nun mit vier Workern ausgeführt wird:

```console
azdata arc postgres server list
```

Wie Sie sehen, ist eine Aufskalierung von drei auf vier Worker erfolgt. 

```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

Wie zuvor können wir erkennen, dass die Servergruppe nun vier Pods verwendet:

```output
kubectl get pods -n arc3

NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01c-0         3/3     Running   0          13h
postgres01w-0         3/3     Running   0          13h
postgres01w-1         3/3     Running   0          13h
postgres01w-2         3/3     Running   0          179m
postgres01w-3         3/3     Running   0          3m13s
```

Die Servergruppe hat jetzt die folgende Form:

|Servergruppenrolle|Servergruppenpod
|----|-----
|Koordinator|postgres01c-0
|Worker|postgres01w-0
|Worker|postgres01w-1
|Worker|postgres01w-2
|Worker|postgres01w-3

Jetzt beschreiben wir den Pod „postgres01w-3“, um zu ermitteln, auf welchem physischen Knoten er gehostet wird:

```console
kubectl describe pod postgres01w-3 -n arc3
```

Beobachten Sie, auf welchen Pods er ausgeführt wird:

|Servergruppenrolle|Servergruppenpod| Pod
|----|-----|------
|Koordinator|postgres01c-0|aks-agentpool-42715708-vmss000000
|Worker|postgres01w-0|aks-agentpool-42715708-vmss000002
|Worker|postgres01w-1|aks-agentpool-42715708-vmss000003
|Worker|postgres01w-2|aks-agentpool-42715708-vmss000000
|Worker|postgres01w-3|aks-agentpool-42715708-vmss000004

Die Architektur sieht wie folgt aus:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/10_kubernetes_schedules_newest_pod.png" alt-text="Kubernetes plant den neuesten Pod auf dem Knoten mit der geringsten Auslastung":::

Kubernetes hat den neuen PostgreSQL-Pod auf dem am wenigsten ausgelasteten physischen Knoten des Kubernetes-Clusters geplant.

## <a name="summary"></a>Zusammenfassung

Um Skalierbarkeit und Leistung der horizontalen Skalierung der Azure Arc-fähigen Servergruppe optimal auszunutzen, sollten Sie folgende Ressourcenkonflikte innerhalb des Kubernetes-Clusters vermeiden:
- Konflikte zwischen der Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe und anderen Workloads, die im gleichen Kubernetes-Cluster gehostet werden
- Konflikte zwischen allen PostgreSQL-Instanzen, die die Azure Arc-fähige PostgreSQL Hyperscale-Servergruppe bilden

Hierzu gibt es mehrere Möglichkeiten:
- Aufskalieren sowohl von Kubernetes als auch der Azure Arc-fähigen PostgreSQL Hyperscale: Skalieren Sie den Kubernetes-Cluster auf dieselbe Weise horizontal wie die Azure Arc-fähige PostgreSQL Hyperscale-Servergruppe. Fügen Sie dem Cluster einen physischen Knoten für jeden Worker hinzu, den Sie der Servergruppe hinzufügen.
- Skalieren Sie den Azure Arc-fähigen PostgreSQL Hyperscale auf, ohne Kubernetes aufzuskalieren: Indem Sie die richtigen Ressourceneinschränkungen (Anforderungen und Grenzwerte für den Arbeitsspeicher und virtuelle Kerne) für die in Kubernetes gehosteten Workloads (einschließlich Azure Arc-fähigem PostgreSQL Hyperscale) festlegen, ermöglichen Sie die Colocation von Workloads auf Kubernetes und verringern das Risiko von Ressourcenkonflikten. Sie müssen sicherstellen, dass die physischen Eigenschaften der physischen Knoten des Kubernetes-Clusters den von Ihnen definierten Ressourceneinschränkungen gerecht werden. Außerdem sollten Sie sicherstellen, dass bei Veränderungen der Workloads im Laufe der Zeit und beim Hinzufügen von Workloads zum Kubernetes-Cluster das Gleichgewicht erhalten bleibt.
- Verwenden Sie die Kubernetes-Mechanismen (Podauswahl, Affinität, Antiaffinität), um die Platzierung der Pods zu beeinflussen.

## <a name="next-steps"></a>Nächste Schritte

[Aufskalieren der Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe durch Hinzufügen weiterer Workerknoten](scale-out-postgresql-hyperscale-server-group.md)
