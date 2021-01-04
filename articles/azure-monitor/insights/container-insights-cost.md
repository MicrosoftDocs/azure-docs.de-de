---
title: Überwachen der Kosten für Azure Monitor für Container | Microsoft-Dokumentation
description: In diesem Artikel wird die Überwachung der Kosten für Metriken und Bestandsdaten beschrieben, die von Azure Monitor für Container gesammelt werden, damit Kunden ihre Nutzung und die entsprechenden Kosten besser verwalten können.
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 81a20f564af68c3da6d63394e4cffe7caed91b46
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96903218"
---
# <a name="understand-monitoring-costs-for-azure-monitor-for-containers"></a>Überwachen der Kosten für Azure Monitor für Container

In diesem Artikel erhalten Sie Informationen rund um die folgenden Kostenfragen zu Azure Monitor für Container:

* Wie kann ich die Kosten vor dem Aktivieren dieser Insight-Funktion abschätzen?

* Wie kann ich die Kosten bemessen, wenn ich Azure Monitor für Container für einen oder mehrere Container aktiviert habe?

* Wie kann ich das Sammeln von Daten verwalten und meine Kosten senken?

In den Azure Monitor-Protokollen werden Daten, die von Ihrem Kubernetes-Cluster generiert werden, gesammelt, indiziert und gespeichert. 

Das Azure Monitor-Preismodell basiert hauptsächlich auf der Datenmenge (in GB), die pro Tag in Ihrem Log Analytics-Arbeitsbereich erfasst wird. Die Kosten eines Log Analytics-Arbeitsbereichs basieren jedoch nicht nur auf dem Umfang der gesammelten Daten, sondern auch auf dem ausgewählten Plan und der Speicherdauer der von den Clustern generierten Daten.

>[!NOTE]
>Alle Größen- und Preisangaben dienen nur als Grundlage für eine Kostenschätzung. Auf der Seite [Preisübersicht](https://azure.microsoft.com/pricing/details/monitor/) für Azure Monitor finden Sie die aktuellen Preise basierend auf Ihrem Azure Monitor Log Analytics-Preismodell und der Azure-Region.

In der folgenden Auflistung werden die Datentypen zusammengefasst, die mit Azure Monitor für Container in einem Kubernetes-Cluster gesammelt werden, die Kosten verursachen und die entsprechend der Nutzung angepasst werden können:

- Die Containerprotokolle „stdout“ und „stderr“ aus allen überwachten Containern in sämtlichen Kubernetes-Namespaces im Cluster.

- Die Containerumgebungsvariablen aus allen überwachten Containern im Cluster.

- Abgeschlossen Kubernetes-Aufträge/-Pods im Cluster, für die keine Überwachung erforderlich ist.

- Das aktive Abrufen von Prometheus-Metriken.

- Die [Sammlung von Diagnoseprotokollen](../../aks/view-master-logs.md) von Kubernetes-Masterknotenprotokollen in Ihrem AKS-Cluster zur Analyse von Protokolldaten, die von Masterkomponenten wie *kube-apiserver* und *kube-controller-manager* generiert werden.

## <a name="what-is-collected-from-kubernetes-clusters"></a>Erfasste Elemente in Kubernetes-Clustern

Azure Monitor für Container enthält vordefinierte Metriken und Bestandselemente, die als Protokolldaten in Ihren Log Analytics-Arbeitsbereich geschrieben werden. Die unten aufgeführten Metriken werden standardmäßig im Minutentakt gesammelt.

### <a name="node-metrics-collected"></a>Gesammelte Knotenmetriken

Die folgenden 24 Metriken werden pro Knoten gesammelt:

- cpuUsageNanoCores
- cpuCapacityNanoCores
- cpuAllocatableNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryCapacityBytes
- memoryAllocatableBytes
- restartTimeEpoch
- used (disk)
- free (disk)
- used_percent (disk)
- io_time (diskio)
- writes (diskio)
- reads (diskio)
- write_bytes (diskio)
- write_time (diskio)
- iops_in_progress (diskio)
- read_bytes (diskio)
- read_time (diskio)
- err_in (net)
- err_out (net)
- bytes_recv (net)
- bytes_sent (net)
- Kubelet_docker_operations (kubelet)

### <a name="container-metrics"></a>Containermetriken

Die folgenden acht Metriken werden pro Container gesammelt:

- cpuUsageNanoCores
- cpuRequestNanoCores
- cpuLimitNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryRequestBytes
- memoryLimitBytes
- restartTimeEpoch

### <a name="cluster-inventory"></a>Clusterbestand

Die folgenden Clusterbestandsdaten werden standardmäßig gesammelt:

- KubePodInventory: pro Container 1/min
- KubeNodeInventory: pro Knoten 1/min
- KubeServices: pro Dienst 1/min
- ContainerInventory: pro Container 1/min

## <a name="estimating-costs-to-monitor-your-aks-cluster"></a>Schätzen der Kosten für die Überwachung des AKS-Clusters

Die nachfolgende Schätzung basiert auf einem AKS-Cluster (Azure Kubernetes Service) mit den angegebenen Beispielgrößen. Zudem gilt die Schätzung nur für gesammelte Metriken und Bestandsdaten. Von der Schätzung ausgeschlossen sind Containerprotokolle („stdout“, „stderr“ und Umgebungsvariablen), die je nach der von der Workload generierten Protokollgröße variieren.

Für die Beispielschätzung wird die Überwachung eines AKS-Clusters mit folgenden Konfigurationen angenommen:

- drei Knoten
- zwei Datenträger pro Knoten
- eine Netzwerkschnittstelle pro Knoten
- zwanzig Pods (ein Container pro Pod = zwanzig Container insgesamt)
- zwei Kubernetes-Namespaces
- fünf Kubernetes Services-Instanzen (einschließlich kube-system-Pods, -Dienste und -Namespaces)
- Sammlungshäufigkeit von 60 Sek. (Standardwert)

Für diese Parameter werden die folgenden Tabellen und Datenmengen pro Stunde im zugewiesenen Log Analytics-Arbeitsbereich generiert. Weitere Informationen zu den einzelnen Tabellen finden Sie unter [Containerdatensätze](container-insights-log-search.md#container-records).

|Tabelle | Geschätzte Größe (MB/Stunde) |
|------|---------------|
|Perf | 12,9 |
|InsightsMetrics | 11,3 |
|KubePodInventory | 1.5 |
|KubeNodeInventory | 0,75 |
|KubeServices | 0,13 |
|ContainerInventory | 3.6 |
|KubeHealth | 0,1 |
|KubeMonAgentEvents |0,005 |

Gesamtmenge: 31 MB/Stunde, ergibt 23,1 GB/Monat (für einen Monat mit 31 Tagen)

Mithilfe der allgemeinen [Preisübersicht](https://azure.microsoft.com/pricing/details/monitor/) für Log Analytics, einem nutzungsbasierten Preismodell, können die monatlichen Kosten für Azure Monitor errechnet werden. Wenn Sie sich für eine Kapazitätsreservierung entscheiden, liegen die monatlichen Kosten je nach ausgewählter Reservierung entsprechend höher.

## <a name="controlling-ingestion-to-reduce-cost"></a>Steuern der Datenerfassung zur Senkung der Kosten

Stellen Sie sich ein Szenario vor, in dem die unterschiedlichen Geschäftseinheiten Ihres Unternehmens die Kubernetes-Infrastruktur und einen Log Analytics-Arbeitsbereich teilen. Dabei werden die Geschäftseinheiten durch Kubernetes-Namespaces voneinander getrennt. Mithilfe des Runbooks für die **Datennutzung**, das in der Dropdownliste **Arbeitsmappen anzeigen** verfügbar ist, können Sie die Datenmengen, die in den einzelnen Arbeitsbereichen erfasst werden, visualisieren.

[![Dropdownliste „Arbeitsmappen anzeigen“](media/container-insights-cost/workbooks-dropdown.png)](media/container-insights-cost/workbooks-dropdown.png#lightbox)


Mithilfe dieser Arbeitsmappe können Sie die Datenquellen visualisieren, ohne selbst eine Bibliothek mit Abfragen basierend auf den in der Dokumentation freigegebenen Informationen erstellen zu müssen. Die Arbeitsmappe enthält Diagramme, mit denen Sie abrechenbare Daten nach folgenden Gesichtspunkten anzeigen können:

- Gesamtmenge der erfassten abrechenbaren Daten (in GB) pro Lösung
- in Containerprotokollen (Anwendungsprotokollen) erfasste abrechenbare Daten
- in einem Kubernetes-Namespace erfasste abrechenbare Containerprotokolldaten
- unter einem Clusternamen erfasste abrechenbare Containerprotokolldaten
- in einem Protokollquelleintrag erfasste abrechenbare Containerprotokolldaten
- in Diagnosemasterknoten-Protokollen erfasste abrechenbare Diagnosedaten

[![Arbeitsmappe für Datennutzung](media/container-insights-cost/data-usage-workbook.png)](media/container-insights-cost/data-usage-workbook.png#lightbox)

Weitere Informationen zum Verwalten von Rechten und Berechtigungen für die Arbeitsmappe finden Sie unter [Zugriffssteuerung](../platform/workbooks-access-control.md).

Wenn Sie analysiert haben, von welcher Quelle bzw. welchen Quellen die meisten Daten generiert werden (oder mehr Daten generiert werden, als Sie benötigen), können Sie die Sammlung von Daten neu konfigurieren. Informationen zur Konfiguration der Sammlung von „stdout“, „stderr“ und Umgebungsvariablen finden Sie im Artikel [Konfigurieren der Datensammlung des Agents](container-insights-agent-config.md).

Die folgenden Änderungen an Ihrem Cluster können Sie beispielsweise durch Ändern der Datei „ConfigMap“ vornehmen, um die Kosten zu senken:

1. Deaktivieren Sie für alle Namespaces im Cluster die stdout-Protokolle, indem Sie folgende Änderung für die Datei „ConfigMap“ ausführen:

    ```
    [log_collection_settings]       
       [log_collection_settings.stdout]          
          enabled = false
    ```

2. Deaktivieren Sie die Sammlung von stderr-Protokollen im Entwicklungsnamespace (z. B. **dev-test**), setzen Sie jedoch deren Sammlung in anderen Namespaces fort (z. B. **prod** und **default**). Führen Sie hierzu die folgende Änderung für die Datei „ConfigMap“ aus:

    >[!NOTE]
    >Die Sammlung von kube-system-Protokollen ist standardmäßig deaktiviert. Behalten Sie die Standardeinstellung bei, und fügen Sie den Namespace **dev-test** der Liste mit Ausschlussnamespaces hinzu, die für die Sammlung von stderr-Protokollen gilt.

    ```
    [log_collection_settings.stderr]          
       enabled = true          
          exclude_namespaces = ["kube-system", "dev-test"]
    ```

3. Deaktivieren Sie die Sammlung von Umgebungsvariablen im gesamten Cluster, indem Sie die folgende Änderung für die Datei „ConfigMap“ ausführen. Diese Änderung gilt für alle Container in sämtlichen Kubernetes-Namespaces. 

    ```
    [log_collection_settings.env_var]
        enabled = false
    ```

4. Geben Sie zur Bereinigung abgeschlossener Aufträge die Bereinigungsrichtlinie in der Auftragsdefinition an, indem Sie die folgende Änderung für die Datei „ConfigMap“ ausführen:

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: pi-with-ttl
    spec:
      ttlSecondsAfterFinished: 100
    ```

Nachdem Sie eine oder mehrere dieser Änderungen auf die Datei „ConfigMaps“ angewendet haben, wenden Sie sie auch auf Ihren Cluster an. Informationen hierzu finden Sie unter [Anwenden der aktualisierten ConfigMap](container-insights-prometheus-integration.md#applying-updated-configmap).

### <a name="prometheus-metrics-scraping"></a>Abfragen von Prometheus-Metriken

Wenn Sie [Prometheus-Metriken](container-insights-prometheus-integration.md) abfragen, berücksichtigen Sie folgende Aspekte, um die Anzahl der im Cluster gesammelten Metriken zu begrenzen:

- Vergewissern Sie sich, dass die Abfragefrequenz optimal festgelegt ist (der Standardwert beträgt 60 Sekunden). Die Abfragehäufigkeit kann bis auf 15 Sekunden erhöht werden. Stellen Sie jedoch sicher, dass die Metriken, die Sie abrufen möchten, auch in dieser Häufigkeit veröffentlicht werden. Andernfalls werden in einem bestimmten Zeitraum viele doppelte Metriken abgerufen und an Ihren Log Analytics-Arbeitsbereich gesendet, was nur zu höheren Kosten für die Datenerfassung und -aufbewahrung, jedoch nicht zu weiteren Vorteilen führt. 

- Azure Monitor für Container unterstützt Ausschluss- und Aufnahmelisten basierend auf dem Namen der Metrik. Wenn Sie beispielsweise in Ihrem Cluster **kubedns**-Metriken abrufen, werden möglicherweise Hunderte davon standardmäßig abgerufen, obwohl Sie etwa nur an einer Teilmenge interessiert sind. Geben Sie eine Liste mit den Metriken an, die abgerufen werden sollen, oder erstellen Sie eine Liste mit ausgeschlossen Metriken, um die Menge der erfassten Daten zu verringern. Das Abrufen von Metriken ist schnell aktiviert. Wenn Sie jedoch nur einen Bruchteil davon auch wirklich nutzen, entstehen unnötige Kosten.

- Filtern Sie beim Abrufen von Podanmerkungen nach Namespaces, damit Sie nicht verwendete Namespaces ausschließen können (etwa den Namespace **dev-test**).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Kostenschätzungen basierend auf aktuellen Verwendungsmustern von Daten, die mit Azure Monitor für Container gesammelt werden, finden Sie unter [Verwalten von Nutzung und Kosten](../platform/manage-cost-storage.md).