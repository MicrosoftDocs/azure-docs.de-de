---
title: Bereitstellen einer Prometheus-Instanz in einem Azure Red Hat OpenShift-Cluster
description: Erstellen einer Prometheus-Instanz auf einem Azure Red Hat OpenShift-Cluster, um die Metriken Ihrer Anwendung zu überwachen.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus, aro, openshift, metriken, red hat
ms.openlocfilehash: 42ed8c90b35eba57fdc3db1f0ed93d44cf9a5e41
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996847"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Bereitstellen einer eigenständigen Prometheus-Instanz in einem Azure Red Hat OpenShift-Cluster

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11 wird zum 30. Juni 2022 eingestellt. Support für die Erstellung neuer Azure Red Hat OpenShift 3.11-Cluster wird bis zum 30. November 2020 bereitgestellt. Nach der Einstellung werden die verbleibenden Azure Red Hat OpenShift 3.11-Cluster abgeschaltet, um Sicherheitsrisiken zu vermeiden.
> 
> Folgen Sie diesem Leitfaden, um [einen Azure Red Hat OpenShift 4-Cluster zu erstellen](tutorial-create-cluster.md).
> Wenn Sie spezielle Fragen haben, [kontaktieren Sie uns](mailto:arofeedback@microsoft.com).

In diesem Artikel wird beschrieben, wie Sie eine eigenständige Prometheus-Instanz mit Dienstermittlung in einem Azure Red Hat OpenShift-Cluster konfigurieren.

> [!NOTE]
> Kundenadministratorzugriff auf den Azure Red Hat OpenShift-Cluster ist nicht erforderlich.

Zielsetup:

- Ein Projekt (prometheus-project) mit Prometheus und Alertmanager.
- Zwei Projekte (app-project1 und app-project2) mit den zu überwachenden Anwendungen.

Sie bereiten einige Prometheus-Konfigurationsdateien lokal vor. Erstellen Sie einen Ordner zum Speichern dieser Dateien. Konfigurationsdateien werden im Cluster für den Fall als Geheimnisse gespeichert, dass dem Cluster später geheime Token hinzugefügt werden.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>Anmelden beim Cluster mithilfe des OC-Tools

1. Öffnen Sie einen Webbrowser, und navigieren Sie dann zur Webkonsole Ihres Clusters (https://openshift.*random-id*.*region*.azmosa.io).
2. Melden Sie sich mit Ihren Azure-Anmeldeinformationen an.
3. Wählen Sie oben rechts Ihren Benutzernamen und dann **Anmeldebefehl kopieren** aus.
4. Fügen Sie Ihren Benutzernamen in das Terminal ein, das Sie verwenden möchten.

> [!NOTE]
> Um zu überprüfen, ob Sie beim richtigen Cluster angemeldet sind, führen Sie den Befehl `oc whoami -c` aus.

## <a name="prepare-the-projects"></a>Vorbereiten der Projekte

Führen Sie die folgenden Befehle aus, um die Projekte zu erstellen:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Sie können entweder den Parameter `-n` oder `--namespace` verwenden oder durch Ausführen des `oc project`-Befehls ein aktives Projekt auswählen.

## <a name="prepare-the-prometheus-configuration-file"></a>Vorbereiten der Prometheus-Konfigurationsdatei
Erstellen Sie eine Datei „prometheus.yml“, indem Sie den folgenden Inhalt eingeben:
```
global:
  scrape_interval: 30s
  evaluation_interval: 5s

scrape_configs:
    - job_name: prom-sd
      scrape_interval: 30s
      scrape_timeout: 10s
      metrics_path: /metrics
      scheme: http
      kubernetes_sd_configs:
      - api_server: null
        role: endpoints
        namespaces:
          names:
          - prometheus-project
          - app-project1
          - app-project2
```
Erstellen Sie ein Geheimnis namens „Prom“, indem Sie die folgende Konfiguration eingeben:
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Die Datei „prometheus. yml“ ist eine grundlegende Prometheus-Konfigurationsdatei. Sie legt die Intervalle fest und konfiguriert die automatische Ermittlung in drei Projekten (prometheus-project, app-project1, app-project2). In der oben genannten Konfigurationsdatei werden die automatisch ermittelten Endpunkte über HTTP ohne Authentifizierung ausgelesen.

Weitere Informationen zum Auslesen von Endpunkten finden Sie unter [Prometheus-Auslesekonfiguration](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config).


## <a name="prepare-the-alertmanager-config-file"></a>Vorbereiten der Alertmanager-Konfigurationsdatei
Erstellen Sie eine Datei „alertmanager.yml“, indem Sie den folgenden Inhalt eingeben:
```
global:
  resolve_timeout: 5m
route:
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 12h
  receiver: default
  routes:
  - match:
      alertname: DeadMansSwitch
    repeat_interval: 5m
    receiver: deadmansswitch
receivers:
- name: default
- name: deadmansswitch
```
Erstellen Sie ein Geheimnis namens „Prom-Alerts“, indem Sie die folgende Konfiguration eingeben:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager.yml ist die Alert Manager-Konfigurationsdatei.

> [!NOTE]
> Führen Sie den Befehl `oc get secret -n prometheus-project` aus, um die beiden vorherigen Schritte zu überprüfen.

## <a name="start-prometheus-and-alertmanager"></a>Starten von Prometheus und Alertmanager
Navigieren Sie zu [openshift/origin repository](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus), und laden Sie die Vorlage [prometheus-standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) herunter. Wenden Sie die Vorlage auf prometheus-project an, indem Sie die folgende Konfiguration eingeben:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Die Datei „prometheus-standalone.yaml“ ist eine OpenShift-Vorlage. Sie erstell eine Prometheus-Instanz vorgeschaltetem mit oauth-proxy sowie eine Alertmanager-Instanz, die ebenfalls durch einen oauth-proxy geschützt ist. In dieser Vorlage wird oauth-proxy so konfiguriert, dass jeder Benutzer zugelassen wird, der den prometheus-project-Namespace „abrufen“ kann (siehe Flag `-openshift-sar`).

> [!NOTE]
> Sie können mit dem Befehl `oc get statefulset -n prometheus-project` überprüfen, ob das StatefulSet „prom“ über gleiche DESIRED- und CURRENT-Nummernreplikate verfügt. Um alle Ressourcen im Projekt zu überprüfen, führen Sie den Befehl `oc get all -n prometheus-project` aus.

## <a name="add-permissions-to-allow-service-discovery"></a>Hinzufügen von Berechtigungen zum Zulassen der Dienstermittlung

Erstellen Sie eine Datei „prometheus-sdrole.yml“, indem Sie den folgenden Inhalt eingeben:
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus service discovery role
    description: |
      Role and rolebinding added permissions required for service discovery in a given project.
    iconClass: fa fa-cogs
    tags: "monitoring,prometheus,alertmanager,time-series"
parameters:
- description: The project name, where a standalone Prometheus is deployed
  name: PROMETHEUS_PROJECT
  value: prometheus-project
objects:
- apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
    name: prometheus-sd
  rules:
  - apiGroups:
    - ""
    resources:
    - services
    - endpoints
    - pods
    verbs:
    - list
    - get
    - watch
- apiVersion: rbac.authorization.k8s.io/v1
  kind: RoleBinding
  metadata:
    name: prometheus-sd
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: Role
    name: prometheus-sd
  subjects:
  - kind: ServiceAccount
    name: prom
    namespace: ${PROMETHEUS_PROJECT}
```
Führen Sie die folgenden Befehle aus, um die Vorlage auf alle Projekte anzuwenden, für die Sie die Dienstermittlung zulassen möchten:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project
```

> [!NOTE]
> Führen Sie die Befehle `oc get role` und `oc get rolebinding` aus, um zu überprüfen, ob „Role“ und „RoleBinding“ ordnungsgemäß erstellt wurden.

## <a name="optional-deploy-example-application"></a>Optional: Bereitstellen einer Beispielanwendung

Alles funktioniert, jedoch sind keine Metrikquellen vorhanden. Navigieren Sie zur Prometheus-URL (https://prom-prometheus-project.apps.*random-id*.*region*.azmosa.io/). Sie ermitteln sie mit dem folgendem Befehl:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Denken Sie daran, das Präfix https:// am Anfang des Hostnamens hinzuzufügen.

Die Seite **Status > Service Discovery** (Status > Dienstermittlung) zeigt 0/0 aktive Ziele an.

Führen Sie unter dem Endpunkt „/metrics“ die folgenden Befehle aus, um eine Beispielanwendung bereitzustellen, die grundlegende Python-Metriken bereitstellt:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Die neuen Anwendungen sollten auf der Seite „Service Discovery“ (Dienstermittlung) innerhalb von 30 Sekunden nach der Bereitstellung als gültige Ziele angezeigt werden.

Um weitere Informationen zu erhalten, wählen Sie **Status** > **Targets** (Status > Ziele) aus.

> [!NOTE]
> Für jedes erfolgreich ausgelesene Ziel fügt Prometheus einen Datenpunkt in der Metrik „up“ hinzu. Klicken Sie in der linken oberen Ecke auf **Prometheus**, geben Sie **up** als Ausdruck ein, und wählen Sie dann **Execute** (Ausführen) aus.

## <a name="next-steps"></a>Nächste Schritte

Sie können benutzerdefinierte Prometheus-Instrumentierung zu Ihren Anwendungen hinzufügen. Die Prometheus-Clientbibliothek, die die Vorbereitung von Prometheus-Metriken vereinfacht, unterstützt verschiedene Programmiersprachen.

Weitere Informationen finden Sie in den folgenden GitHub-Bibliotheken:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Go](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
