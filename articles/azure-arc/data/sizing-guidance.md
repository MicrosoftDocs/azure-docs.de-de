---
title: Dimensionierungsleitfaden
description: Planen Sie die Bereitstellungsgröße von Azure Arc-fähigen Datendiensten.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 6e7f2e445c3e4e8df7420c0587e156968f3a2c92
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542676"
---
# <a name="sizing-guidance"></a>Dimensionierungsleitfaden

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-sizing-guidance"></a>Übersicht über den Dimensionierungsleitfaden

Bei der Planung der Bereitstellung von Azure Arc-Datendiensten sollten Sie den richtigen Umfang der erforderlichen Compute-, Arbeitsspeicher- und Speicherkapazität zum Ausführen des Azure Arc-Datencontrollers und die Anzahl der verwalteten SQL-Instanzen und PostgreSQL Hyperscale-Servergruppen planen, die Sie bereitstellen.  Da Azure Arc-fähige Datendienste in Kubernetes bereitgestellt werden, können Sie dem Kubernetes-Cluster im Laufe der Zeit flexibel zusätzliche Computeknoten oder zusätzlichen Speicher hinzufügen, um die Kapazität zu erhöhen.  Dieser Leitfaden enthält Hinweise zu den Mindestanforderungen und den empfohlenen Größen für häufige Anforderungen.

## <a name="general-sizing-requirements"></a>Allgemeine Dimensionierungsanforderungen

> [!NOTE]
> Wenn Sie mit den Konzepten in diesem Artikel nicht vertraut sind, informieren Sie sich über die [Kubernetes-Ressourcenkontrolle](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) und die [Größeneinheiten in Kubernetes](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes).

Die Anzahl der Kerne muss eine ganze Zahl größer oder gleich 1 sein.

Bei der Verwendung von azdata für die Bereitstellung sollten die Arbeitsspeicherwerte als Zweierpotenz angegeben werden, d. h. mit dem Suffix Ki, Mi oder Gi.

Grenzwerte müssen immer größer als der Anforderungswert sein, sofern angegeben.

Die Grenzwerte für Kerne sind die abrechenbare Metrik für verwaltete SQL-Instanzen und PostgreSQL Hyperscale-Servergruppen.

## <a name="minimum-deployment-requirements"></a>Mindestanforderungen für die Bereitstellung

Eine der Mindestgrößen für die Bereitstellung Azure Arc-fähiger Datendienste umfasst einen Azure Arc-Datencontroller in Kombination mit einer verwalteten SQL-Instanz und einer PostgreSQL Hyperscale-Servergruppe mit zwei Workerknoten.  Für diese Konfiguration benötigen Sie mindestens 16 GB RAM und 4 Kerne als _verfügbare_ Kapazität im Kubernetes-Cluster.  Sie sollten sicherstellen, dass die Kubernetes-Knoten eine Mindestgröße von 8 GB RAM und 4 Kernen umfassen und dass auf allen Kubernetes-Knoten eine Gesamtkapazität von 16 GB RAM zur Verfügung steht.  Beispielsweise können Sie über einen Knoten mit 32 GB RAM und 4 Kernen oder über zwei Knoten mit jeweils 16 GB RAM und vier Kernen verfügen.

Ausführliche Informationen über die Speicherdimensionierung finden Sie im Artikel zur [Speicherkonfiguration](storage-configuration.md).

## <a name="data-controller-sizing-details"></a>Dimensionierungsdetails des Datencontrollers

Der Datencontroller ist eine Sammlung von Pods, die im Kubernetes-Cluster bereitgestellt werden, um eine API, den Controllerdienst, den Bootstrapper sowie die Überwachungsdatenbanken und -dashboards zur Verfügung zu stellen.  In dieser Tabelle werden die Standardwerte für Arbeitsspeicher- und CPU-Anforderungen sowie CPU-Limits beschrieben.

|Podname|CPU-Anforderung|Arbeitsspeicheranforderung|CPU-Limit|Arbeitsspeicherlimit|Hinweise|
|---|---|---|---|---|---|
|**bootstrapper**|100m|100Mi|200m|200Mi||
|**control**|400m|2Gi|1800m|2Gi||
|**controldb**|200m|3Gi|800m|6Gi||
|**controlwd**|10m|100Mi|100m|200Mi||
|**logsdb**|200m|1600Mi|2|1600Mi||
|**logsui**|100m|500Mi|2|2Gi||
|**metricsdb**|200m|800Mi|400m|2Gi||
|**metricsdc**|100m|200Mi|200m|300Mi|„metricsdc“ ist ein daemonset, das auf jedem Kubernetes-Knoten im Cluster erstellt wird.  Die Zahlen in der Tabelle gelten _pro Knoten_. Wenn Sie in der Bereitstellungsprofildatei vor dem Erstellen des Datencontrollers „allownodemetricscollection = false“ festlegen, wird das daemonset „metricsdc“ nicht erstellt.|
|**metricsui**|20m|200Mi|500m|200Mi||
|**mgmtproxy**|200m|250Mi|500m|500Mi||

Sie können die Standardeinstellungen für die Pods „controldb“ und „control“ in der Bereitstellungsprofildatei oder der Datencontroller-YAML-Datei überschreiben.  Beispiel:

```yaml
  resources:
    controller:
      limits:
        cpu: "1000m"
        memory: "3Gi"
      requests:
        cpu: "800m"
        memory: "2Gi"
    controllerDb:
      limits:
        cpu: "800m"
        memory: "8Gi"
      requests:
        cpu: "200m"
        memory: "4Gi"
```

Ausführliche Informationen über die Speicherdimensionierung finden Sie im Artikel zur [Speicherkonfiguration](storage-configuration.md).

## <a name="sql-managed-instance-sizing-details"></a>Dimensionierungsdetails verwalteter SQL-Instanzen

Jede verwaltete SQL-Instanz muss über die folgenden Ressourcenmindestanforderungen verfügen:
- Memory: 2Gi
- Kerne: 1

Jeder erstellte SQL Managed Instance-Pod umfasst drei Container:

|Containername|CPU-Anforderung|Arbeitsspeicheranforderung|CPU-Limit|Arbeitsspeicherlimit|Hinweise|
|---|---|---|---|---|---|
|fluentbit|100m|100Mi|Nicht angegeben|Nicht angegeben|Die fluentbit-Containerressourcenanforderungen gelten _zusätzlich_ zu den für die verwaltete SQL-Instanz angegebenen Anforderungen.||
|arc-sqlmi|Vom Benutzer angegeben oder nicht angegeben|Vom Benutzer angegeben oder nicht angegeben|Vom Benutzer angegeben oder nicht angegeben|Vom Benutzer angegeben oder nicht angegeben||
|collectd|Nicht angegeben|Nicht angegeben|Nicht angegeben|Nicht angegeben||

Die Standardvolumegröße für alle persistenten Volumes beträgt 5gi.

## <a name="postgresql-hyperscale-server-group-sizing-details"></a>Dimensionierungsdetails von PostgreSQL Hyperscale-Servergruppen

Jeder PostgreSQL Hyperscale-Servergruppenknoten muss über die folgenden minimalen Ressourcenanforderungen verfügen:
- Memory: 256Mi
- Kerne: 1

Jeder erstellte PostgreSQL Hyperscale-Servergruppenkoordinator-Pod oder -Workerpod umfasst drei Container:

|Containername|CPU-Anforderung|Arbeitsspeicheranforderung|CPU-Limit|Arbeitsspeicherlimit|Hinweise|
|---|---|---|---|---|---|
|fluentbit|100m|100Mi|Nicht angegeben|Nicht angegeben|Die fluentbit-Containerressourcenanforderungen gelten _zusätzlich_ zu den für die PostgreSQL Hyperscale-Servergruppenknoten angegebenen Anforderungen.|
|postgres|Vom Benutzer angegeben oder nicht angegeben|Vom Benutzer angegeben oder 256Mi (Standard)|Vom Benutzer angegeben oder nicht angegeben|Vom Benutzer angegeben oder nicht angegeben||
|telegraf|Nicht angegeben|Nicht angegeben|Nicht angegeben|Nicht angegeben||

## <a name="cumulative-sizing"></a>Kumulative Dimensionierung

Die erforderliche Gesamtgröße einer Umgebung für Azure Arc-fähige Datendienste hängt hauptsächlich von der Anzahl und Größe der zu erstellenden Datenbankinstanzen ab.  Die Gesamtgröße kann schwierig vorherzusagen sein, da die Anzahl der Instanzen zunimmt und abnimmt und sich der Umfang der für jede Datenbankinstanz erforderlichen Ressourcen ändert.

Die Baselinegröße einer Umgebung für Azure Arc-fähige Datendienste ist die Größe des Datencontrollers, der 4 Kerne und 16 GB RAM erfordert.  Dieser können Sie die Summe der Kerne und des Arbeitsspeichers hinzufügen, die für die Datenbankinstanzen erforderlich sind.  Für SQL Managed Instance ist die Anzahl der Pods gleich der Anzahl der zu erstellenden verwalteten SQL-Instanzen.  Für PostgreSQL Hyperscale-Servergruppen entspricht die Anzahl der Pods der Anzahl der Workerknoten plus einem Koordinatorknoten.  Beispielsweise umfasst eine PostgreSQL-Servergruppe mit 3 Workerknoten insgesamt 4 Pods.

Zusätzlich zu den Kernen und dem Arbeitsspeicher, die Sie für jede Datenbankinstanz anfordern, sollten Sie 250m an Kernen und 250Mi an Arbeitsspeicher für die Agentcontainer hinzufügen.

Im Anschluss finden Sie eine Beispielberechnung der Dimensionierung.

Anforderungen:

- **SQL1**: 1 verwaltete SQL-Instanz mit 16 GB RAM und 4 Kernen
- **SQL2**: 1 verwaltete SQL-Instanz mit 256 GB RAM und 16 Kernen
- **Postgres1**: 1 PostgreSQL Hyperscale-Servergruppe, die 4 Worker mit jeweils 12 GB RAM und 4 Kerne umfasst

Dimensionierungsberechnungen:

- Größe von SQL1: 1 Pod * ([16Gi RAM, 4 Kerne] + [250Mi RAM, 250m Kerne]) für die Agents pro Pod = 16,25Gi RAM, 4,25 Kerne.
- Größe von SQL2: 1 Pod * ([256Gi RAM, 16 Kerne] ++ [250Mi RAM, 250m Kerne]) für die Agents pro Pod = 256,25Gi RAM, 16,25 Kerne.
- Gesamtgröße von SQL1 und SQL2: (16,25GB + 256,25Gi) = 272,5Gi RAM, (4,25 Kerne + 16,25 Kerne) = 20,5 Kerne.

- Größe von Postgres1: (4 Workerpods + 1 Koordinatorpod) * ([12 GB RAM, 4 Kerne] + [250Mi RAM, 250m Kerne]) für die Agents pro Pod = 61,25 GB RAM, 21,25 Kerne.

- Die für die Datenbankinstanzen erforderliche Gesamtkapazität: 272,5 GB RAM, 20,5 Kerne für SQL + 61,25 GB RAM, 21,25 Kerne für PostgreSQL Hyperscale = 333,75 GB RAM, 42,5 Kerne.

- Die für die Datenbankinstanzen plus Datencontroller erforderliche Gesamtkapazität: 333,75 GB RAM, 42,5 Kerne für die Datenbankinstanzen + 16 GB RAM, 4 Kerne für den Datencontroller = 349,75 GB RAM, 46,5 Kerne.

Ausführliche Informationen über die Speicherdimensionierung finden Sie im Artikel zur [Speicherkonfiguration](storage-configuration.md).

## <a name="other-considerations"></a>Weitere Überlegungen

Beachten Sie, dass die Anforderung der Datenbankinstanzgröße für Kerne oder RAM die verfügbare Kapazität der Kubernetes-Knoten im Cluster nicht überschreiten darf.  Wenn beispielsweise der größte Kubernetes-Knoten im Kubernetes-Cluster 256 GB RAM und 24 Kerne umfasst, können Sie keine Datenbankinstanz mit einer Anforderung von 512 GB RAM und 48 Kernen erstellen.  

Es empfiehlt sich, auf den Kubernetes-Knoten stets über mindestens 25 % der verfügbaren Kapazität zu verfügen. Dies ermöglicht die effiziente Planung der Erstellung von Pods sowie eine flexible Skalierung und ein langfristigeres bedarfsabhängiges Wachstum.  

Berücksichtigen Sie bei den Dimensionierungsberechnungen die Ressourcenanforderungen der Kubernetes-Systempods und andere Workloads, die möglicherweise gemeinsam mit Azure Arc-fähigen Datendiensten in demselben Kubernetes-Cluster Kapazität nutzen.

Damit während geplanter Wartung und Notfällen Hochverfügbarkeit erhalten bleibt, sollten Sie bei der Planung berücksichtigen, dass stets eine Situation eintreten kann, in der mindestens ein Kubernetes-Knoten im Cluster nicht verfügbar ist.  Kubernetes versucht, die Pods, die auf einem wegen Wartung oder aufgrund eines Fehlers außer Betrieb genommenen Knoten ausgeführt wurden, neu zu planen.  Falls auf den verbleibenden Knoten keine Kapazität verfügbar ist, wird die Erstellung dieser Pods erst dann neu geplant, wenn wieder Kapazität verfügbar ist.  Große Datenbankinstanzen erfordern besondere Vorsicht.  Wenn beispielsweise nur ein Kubernetes-Knoten groß genug ist, um die Ressourcenanforderungen einer großen Datendankinstanz zu erfüllen, und dieser Knoten ausfällt, kann Kubernetes diesen Datenbankinstanzpod nicht für die Erstellung auf einem anderen Kubernetes-Knoten planen.

Beachten Sie die [oberen Grenzwerte für die Kubernetes-Clustergröße](https://kubernetes.io/docs/setup/best-practices/cluster-large/).

Der Kubernetes-Administrator hat möglicherweise [Ressourcenkontingente](https://kubernetes.io/docs/concepts/policy/resource-quotas/) für Ihren Namespace bzw. Ihr Projekt eingerichtet.  Beachten Sie diese beim Planen der Datenbankinstanzgrößen.
