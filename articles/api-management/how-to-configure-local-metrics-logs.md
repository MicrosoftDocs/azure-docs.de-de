---
title: Konfigurieren von lokalen Metriken und Protokollen für selbstgehostete Gateways für Azure API Management | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie lokale Metriken und Protokolle für selbstgehostete Gateways von Azure API Management in einem Kubernetes-Cluster konfigurieren.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/01/2021
ms.author: apimpm
ms.openlocfilehash: e34c25b2e3bfa845e258dc5d9699497d7ffcb004
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526669"
---
# <a name="configure-local-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>Konfigurieren von lokalen Metriken und Protokollen für selbstgehostete Gateways für Azure API Management

Dieser Artikel enthält Details zum Konfigurieren lokaler Metriken und Protokolle für das [selbstgehostete Gateway](./self-hosted-gateway-overview.md), das in einem Kubernetes-Cluster bereitgestellt ist. Informationen zum Konfigurieren von Cloudmetriken und -protokollen finden Sie in [diesem Artikel](how-to-configure-cloud-metrics-logs.md). 

## <a name="metrics"></a>Metriken
Das selbstgehostete Gateway unterstützt [StatsD](https://github.com/statsd/statsd), das zu einem Standardprotokoll für die Erfassung und Aggregation von Metriken geworden ist. In diesem Abschnitt werden die Schritte für die Bereitstellung von StatsD in Kubernetes, die Konfiguration des Gateways zur Ausgabe von Metriken über StatsD und die Verwendung von [Prometheus](https://prometheus.io/) zum Überwachen der Metriken erläutert. 

### <a name="deploy-statsd-and-prometheus-to-the-cluster"></a>Bereitstellen von StatsD und Prometheus im Cluster

Es folgt eine YAML-Beispielkonfiguration für die Bereitstellung von StatsD und Prometheus im Kubernetes-Cluster, in dem ein selbstgehostetes Gateway bereitgestellt wird. Außerdem wird für beide ein [Dienst](https://kubernetes.io/docs/concepts/services-networking/service/) erstellt. Das selbstgehostete Gateway veröffentlicht Metriken im StatsD-Dienst. Der Zugriff auf das Prometheus-Dashboard erfolgt über den Dienst.   

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: sputnik-metrics-config
data:
  statsd.yaml: ""
  prometheus.yaml: |
    global:
      scrape_interval:     3s
      evaluation_interval: 3s
    scrape_configs:
      - job_name: 'prometheus'
        static_configs:
          - targets: ['localhost:9090']
      - job_name: 'test_metrics'
        static_configs:
          - targets: ['localhost:9102']
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sputnik-metrics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sputnik-metrics
  template:
    metadata:
      labels:
        app: sputnik-metrics
    spec:
      containers:
      - name: sputnik-metrics-statsd
        image: mcr.microsoft.com/aks/hcp/prom/statsd-exporter
        ports:
        - name: tcp
          containerPort: 9102
        - name: udp
          containerPort: 8125
          protocol: UDP
        args:
          - --statsd.mapping-config=/tmp/statsd.yaml
          - --statsd.listen-udp=:8125
          - --web.listen-address=:9102
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      - name: sputnik-metrics-prometheus
        image: mcr.microsoft.com/oss/prometheus/prometheus
        ports:
        - name: tcp
          containerPort: 9090
        args:
          - --config.file=/tmp/prometheus.yaml
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      volumes:
        - name: sputnik-metrics-config-files
          configMap:
            name: sputnik-metrics-config
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-statsd
spec:
  type: NodePort
  ports:
  - name: udp
    port: 8125
    targetPort: 8125
    protocol: UDP
  selector:
    app: sputnik-metrics
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-prometheus
spec:
  type: LoadBalancer
  ports:
  - name: http
    port: 9090
    targetPort: 9090
  selector:
    app: sputnik-metrics
```

Speichern Sie die Konfigurationen in einer Datei mit dem Namen `metrics.yaml`, und stellen Sie mithilfe des folgenden Befehls alles im Cluster bereit:

```console
kubectl apply -f metrics.yaml
```

Führen Sie nach Abschluss der Bereitstellung den folgenden Befehl aus, um zu prüfen, ob die Pods ausgeführt werden. Beachten Sie, dass Ihr Podname anders ist. 

```console
kubectl get pods
NAME                                   READY   STATUS    RESTARTS   AGE
sputnik-metrics-f6d97548f-4xnb7        2/2     Running   0          1m
```

Führen Sie den folgenden Befehl aus, um zu prüfen, ob die Dienste ausgeführt werden. Notieren Sie `CLUSTER-IP` und `PORT` des StatsD-Diensts. Diese Angaben benötigen wir später. Sie können das Prometheus-Dashboard besuchen, indem Sie dessen `EXTERNAL-IP` und `PORT` angeben.

```console
kubectl get services
NAME                         TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE
sputnik-metrics-prometheus   LoadBalancer   10.0.252.72   13.89.141.90    9090:32663/TCP               18h
sputnik-metrics-statsd       NodePort       10.0.41.179   <none>          8125:32733/UDP               18h
```

### <a name="configure-the-self-hosted-gateway-to-emit-metrics"></a>Konfigurieren des selbstgehosteten Gateways zum Ausgeben von Metriken

Nachdem StatsD und Prometheus bereitgestellt wurden, können wir die Konfigurationen des selbstgehosteten Gateways so aktualisieren, dass die Ausgabe von Metriken über StatsD beginnt. Das Feature kann über den Schlüssel `telemetry.metrics.local` in der ConfigMap in der selbstgehosteten Gatewaybereitstellung mit zusätzlichen Optionen aktiviert oder deaktiviert werden. Es folgt eine Übersicht über die verfügbaren Optionen:

| Feld  | Standard | Beschreibung |
| ------------- | ------------- | ------------- |
| telemetry.metrics.local  | `none` | Aktiviert die Protokollierung durch StatsD. Mögliche Werte: `none` oder `statsd`. |
| telemetry.metrics.local.statsd.endpoint  | – | Gibt den StatsD-Endpunkt an. |
| telemetry.metrics.local.statsd.sampling  | – | Gibt die Stichprobenhäufigkeit für Metriken an. Der Wert kann im Bereich 0 bis 1 liegen. Beispiel: `0.5`|
| telemetry.metrics.local.statsd.tag-format  | – | [Taggingformat](https://github.com/prometheus/statsd_exporter#tagging-extensions) der Exportfunktion von StatsD. Mögliche Werte: `none`, `librato`, `dogStatsD`, `influxDB`. |

Es folgt eine Beispielkonfiguration:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.metrics.local: "statsd"
        telemetry.metrics.local.statsd.endpoint: "10.0.41.179:8125"
        telemetry.metrics.local.statsd.sampling: "1"
        telemetry.metrics.local.statsd.tag-format: "dogStatsD"
```

Aktualisieren Sie die YAML-Datei der selbstgehosteten Gatewaybereitstellung mit den obigen Konfigurationen, und übernehmen Sie die Änderungen mit dem nachstehenden Befehl: 

```console
kubectl apply -f <file-name>.yaml
 ```

Um die jüngsten Konfigurationsänderungen zu übernehmen, starten Sie die Gatewaybereitstellung mit dem folgenden Befehl neu:

```console
kubectl rollout restart deployment/<deployment-name>
```

### <a name="view-the-metrics"></a>Anzeigen der Metriken

Nachdem wir alles eingerichtet und konfiguriert haben, sollte das selbstgehostete Gateway über StatsD Metriken melden. Prometheus übernimmt die Metriken aus StatsD. Wechseln Sie zum Prometheus-Dashboard durch Angeben von `EXTERNAL-IP` und `PORT` des Prometheus-Diensts. 

Rufen Sie die API über das selbstgehostete Gateway auf. Wenn alles richtig konfiguriert ist, sollten Sie die folgenden Metriken anzeigen können:

| Metrik  | Beschreibung |
| ------------- | ------------- |
| Requests  | Anzahl der API-Anforderungen innerhalb des Zeitraums |
| DurationInMS | Anzahl von Millisekunden zwischen dem Zeitpunkt, zu dem das Gateway die Anforderung empfangen hat, und dem Zeitpunkt, zu dem die Antwort vollständig gesendet wurde |
| BackendDurationInMS | Anzahl von Millisekunden für alle Back-End-E/A-Vorgänge (Verbindungsherstellung, Senden und Empfangen von Bytes)  |
| ClientDurationInMS | Anzahl von Millisekunden für alle Client-E/A-Vorgänge (Verbindungsherstellung, Senden und Empfangen von Bytes)  |

## <a name="logs"></a>Logs

Das selbstgehostete Gateway gibt Protokolle standardmäßig in `stdout` und `stderr` aus. Sie können die Protokolle mit dem folgenden Befehl mühelos einsehen:

```console
kubectl logs <pod-name>
```

Wenn Ihr selbstgehostetes Gateway in Azure Kubernetes Service bereitgestellt ist, können Sie [Azure Monitor für Container](../azure-monitor/insights/container-insights-overview.md) aktivieren, um `stdout` und `stderr` aus Ihren Workloads zu sammeln und die Protokolle in Log Analytics anzuzeigen. 

Das selbstgehostete Gateway unterstützt auch eine Reihe von Protokollen, einschließlich `localsyslog`, `rfc5424` und `journal`. In der folgenden Tabelle sind alle unterstützten Optionen zusammengefasst. 

| Feld  | Standard | Beschreibung |
| ------------- | ------------- | ------------- |
| telemetry.logs.std  | `text` | Ermöglicht die Protokollierung in Standarddatenströmen. Möglicher Wert: `none`, `text`, `json` |
| telemetry.logs.local  | `none` | Aktiviert die lokale Protokollierung. Möglicher Wert: `none`, `auto`, `localsyslog`, `rfc5424`, `journal`  |
| telemetry.logs.local.localsyslog.endpoint  | – | Gibt den Endpunkt für localsyslog an.  |
| telemetry.logs.local.localsyslog.facility  | – | Gibt den [Facilitycode](https://en.wikipedia.org/wiki/Syslog#Facility) für localsyslog an. Beispiel: `7` 
| telemetry.logs.local.rfc5424.endpoint  | – | Gibt den rfc5424-Endpunkt an.  |
| telemetry.logs.local.rfc5424.facility  | – | Gibt den Facilitycode gemäß [rfc5424](https://tools.ietf.org/html/rfc5424) an. Beispiel: `7`  |
| telemetry.logs.local.journal.endpoint  | – | Gibt den Endpunkt des Journals an.  |

Es folgt eine Beispielkonfiguration für die lokale Protokollierung:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.logs.std: "text"
        telemetry.logs.local.localsyslog.endpoint: "/dev/log"
        telemetry.logs.local.localsyslog.facility: "7"
```
 
## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum selbstgehosteten Gateway finden Sie unter [Azure API Management: Übersicht über das selbstgehostete Gateway](self-hosted-gateway-overview.md).
* Erfahren Sie mehr zum [Konfigurieren und Speichern von Protokollen in der Cloud](how-to-configure-local-metrics-logs.md).
