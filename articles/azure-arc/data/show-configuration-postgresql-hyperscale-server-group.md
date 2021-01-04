---
title: Anzeigen der Konfiguration einer Arc-fähigen PostgreSQL Hyperscale-Servergruppe
titleSuffix: Azure Arc enabled data services
description: Anzeigen der Konfiguration einer Arc-fähigen PostgreSQL Hyperscale-Servergruppe
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a268cd6b2fa3da6846554e3d1b170298abec7f18
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93279400"
---
# <a name="show-the-configuration-of-an-arc-enabled-postgresql-hyperscale-server-group"></a>Anzeigen der Konfiguration einer Arc-fähigen PostgreSQL Hyperscale-Servergruppe

In diesem Artikel wird erläutert, wie Sie die Konfiguration Ihrer Servergruppe(n) anzeigen. Dies geschieht durch die Beantwortung einiger Fragen, die Sie sich möglicherweise bereits selbst gestellt haben. Manchmal gibt es mehrere gültige Antworten. In diesem Artikel werden die gängigsten oder nützlichsten Artikel erläutert. Diese Fragen werden nach Themen gruppiert:

- Aus Sicht von Kubernetes
- aus Sicht eines Azure Arc-fähigen Datendiensts

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-a-kubernetes-point-of-view"></a>Aus Sicht von Kubernetes

### <a name="how-many-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale"></a>Wie viele Pods werden von der Azure Arc-aktivierten PostgreSQL Hyperscale-Servergruppe verwendet?

Listen Sie die Kubernetes-Ressourcen vom Typ „Postgres“ auf. Führen Sie den folgenden Befehl aus:

```console
kubectl get postgresqls [-n <namespace name>]
```

In der Ausgabe dieses Befehls wird die Liste der erstellten Servergruppen angezeigt. Für jede Servergruppe wird die Anzahl der Pods angegeben. Zum Beispiel:

```output
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      6h34m
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
```

Dieses Beispiel veranschaulicht, dass zwei Servergruppen erstellt werden und dass jede auf drei Pods ausgeführt wird (1 Koordinator + 2 Worker). Das bedeutet, dass die Servergruppen, die in diesem Azure Arc-Datencontroller erstellt wurden, sechs Pods verwenden.

### <a name="what-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Welche Pods werden von Azure Arc-aktivierten PostgreSQL Hyperscale-Servergruppen verwendet?

Führen Sie Folgendes aus:

```console
kubectl get pods [-n <namespace name>]
```

Die Liste der Pods wird zurückgegeben. Die Pods, die von Ihren Servergruppen verwendet werden, werden basierend auf den Namen angezeigt, die Sie diesen Servergruppen gegeben haben. Zum Beispiel:

```console 
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-vdltm   1/1     Running   0          6d8h
control-h6kc9        2/2     Running   0          6d8h
controldb-0          2/2     Running   0          6d8h
controlwd-96sbn      1/1     Running   0          6d8h
logsdb-0             1/1     Running   0          6d8h
logsui-7wkg2         1/1     Running   0          6d8h
metricsdb-0          1/1     Running   0          6d8h
metricsdc-28ffl      1/1     Running   0          6d8h
metricsui-k7qsh      1/1     Running   0          6d8h
mgmtproxy-gd84z      2/2     Running   0          6d8h
postgres01-0         3/3     Running   0          6h50m
postgres01-1         3/3     Running   0          6h50m
postgres01-2         3/3     Running   0          6h50m
postgres02-0         3/3     Running   0          22h
postgres02-1         3/3     Running   0          22h
postgres02-2         3/3     Running   0          22h
```

In diesem Beispiel werden die beiden erstellten Servergruppen von den folgenden sechs Pods gehostet:
- `postgres01-0`
- `postgres01-1`
- `postgres01-2`
- `postgres02-0`
- `postgres02-1`
- `postgres02-2`  

### <a name="what-server-group-pod-is-used-for-what-role-the-server-group"></a>Welcher Servergruppenpod wird für welche Rolle der Servergruppe verwendet?

Jeder Podname, der mit dem Suffix `-0` versehen ist, stellt einen Koordinatorknoten dar. Jeder Knotenname, der mit dem Suffix `-x` versehen und 1 oder größer ist, ist ein Workerknoten. Im obigen Beispiel:
- Die Koordinatoren sind: `postgres01-0`, `postgres02-0`
- Die Worker sind: `postgres01-2`, `postgres01-2`, `postgres02-1`, `postgres02-2`

### <a name="what-is-the-status-of-the-pods"></a>Wie lautet der Status der Pods?

Führen Sie `kubectl get pods` aus, und sehen Sie sich die Spalte `STATUS` an.

### <a name="what-persistent-volume-claims-pvcs-are-being-used"></a>Welche persistenten Volumeansprüche (Persistent Volume Claims, PVCs) werden verwendet? 

Um zu verstehen, welche PVCs allgemein und welche PVCs für Daten, Protokolle und Sicherungen verwendet werden, führen Sie Folgendes aus: 

```console
kubectl get pvc [-n <namespace name>]
```

Standardmäßig gibt das Präfix des Namens eines PVCs seine Verwendung an:

- `backups-`...: ist ein für Sicherungen verwendeter PVC
- `data-`...: ist ein für Datendateien verwendeter PVC
- `logs-`...: ist ein für Transaktionsprotokolle/WAL-Dateien verwendeter PVC

Zum Beispiel:

```output
NAME                                            STATUS   VOLUME              CAPACITY   ACCESS MODES   STORAGECLASS    AGE
backups-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    local-pv-485e37db   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    local-pv-9d3d4a15   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    local-pv-7b8dd819   1938Gi     RWO            local-storage   6d6h
...
data-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-3c1a8cc5   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-8303ab19   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-55572fe6   1938Gi     RWO            local-storage   6d6h
...
logs-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-5e852b76   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-55d309a7   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-5ccd02e6   1938Gi     RWO            local-storage   6d6h
...
```


## <a name="from-an-azure-arc-enabled-data-services-point-of-view"></a>Aus Sicht eines Azure Arc-fähigen Datendiensts:

* Wie viele Servergruppen werden in einem Arc-Datencontroller erstellt?
* Was lauten ihre Namen?
* Wie viele Workerknoten werden von ihnen verwendet?
* Welche Version von Postgres führen sie aus?

Verwenden Sie einen der folgenden Befehle.
- **Mit kubectl:**

   ```console
   kubectl get postgresqls [-n <namespace name>]
   ``` 

   Beispielsweise wird die folgende Ausgabe generiert, wobei jede Zeile eine `servergroup` angibt. Die Struktur des Namens in der folgenden Abbildung wird wie folgt gebildet:

   `<Name-Of-Custom-Resource-Definition>`/`<Server-Group-Name>`

   ```output
   NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
   postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      7h15m
   postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
   ```

   In der Ausgabe oben werden 2 Servergruppen mit Postgres Version 12 aufgeführt. Wenn die Version Postgres 11 ist, lautet der Name der CRD stattdessen postgresql-11.arcdata.microsoft.com.

   Jede Servergruppe wird auf 3 Knoten/Pods ausgeführt: 1 Koordinator und 2 Worker.

- **Mit azdata:**

Führen Sie den folgenden Befehl aus. Die Ausgabe zeigt ähnliche Informationen wie kubectl an:

   ```console
   azdata arc postgres server list

   `output
   Name        State    Workers
   ----------  -------  ---------
   postgres01  Ready    2
   postgres02  Ready    2
   ```


### <a name="how-much-memory-and-vcores-are-being-used-and-what-extensions-were-created-for-a-group"></a>Wie viel Arbeitsspeicher und wie viele virtuelle Kerne werden verwendet, und welche Erweiterungen wurden für eine Gruppe erstellt?

Führen Sie einen der folgenden Befehlen aus.

**Mit Kubectl:**

Verwenden Sie kubectl, um eine Postgres-Ressource zu beschreiben. Zu diesem Zweck benötigen Sie den Typ (Name der Kubernetes-Ressource (CRD) für die entsprechende Postgres-Version, wie oben gezeigt) und den Namen der Servergruppe.
Das allgemeine Format des Befehls lautet folgendermaßen:

```console
kubectl describe <CRD name>/<server group name> [-n <namespace name>]
```

Zum Beispiel:

```console
kubectl describe postgresql-12/postgres02
```

Mit diesem Befehl wird die Konfiguration der Servergruppe angezeigt:

```output
Name:         postgres02
Namespace:    arc
Labels:       <none>
Annotations:  <none>
API Version:  arcdata.microsoft.com/v1alpha1
Kind:         postgresql-12
Metadata:
  Creation Timestamp:  2020-08-31T21:01:07Z
  Generation:          10
  Resource Version:    569516
  Self Link:           /apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02
  UID:                 8a9cd118-361b-4a2e-8a9d-5f9257bf6abb
Spec:
  Engine:
    Extensions:
      Name:  citus
      Name:  pg_stat_statements
  Scale:
    Workers:  2
  Scheduling:
    Default:
      Resources:
        Limits:
          Cpu:     4
          Memory:  1024Mi
        Requests:
          Cpu:     1
          Memory:  512Mi
  Service:
    Type:  NodePort
  Storage:
    Data:
      Class Name:  local-storage
      Size:        5Gi
    Logs:
      Class Name:  local-storage
      Size:        5Gi
Status:
  External Endpoint:  10.0.0.4:31066
  Ready Pods:         3/3
  State:              Ready
Events:               <none>
```

>[!NOTE]
>Vor dem Release von Oktober 2020 war `Workers` im vorherigen Beispiel `Shards`. Weitere Informationen finden Sie unter [Versionshinweise – Azure Arc-fähige Datendienste (Vorschauversion)](release-notes.md).

Benennen wir einige bestimmte relevante Punkte in der Beschreibung der oben gezeigten `servergroup`. Welche Informationen erhalten wir zu dieser Servergruppe?

- Es handelt sich um Version 12 von Postgres: 
   > ```json
   > Kind:         `postgresql-12`
   > ```
- Erstellungsdatum ist der Monat August 2020:
   > ```json
   > Creation Timestamp:  `2020-08-31T21:01:07Z`
   > ```
- In dieser Servergruppe wurden zwei Postgres-Erweiterungen erstellt: `citus` und `pg_stat_statements`
   > ```json
   > Engine:
   >    Extensions:
   >      Name:  `citus`
   >      Name:  `pg_stat_statements`
   > ```
- Es werden zwei Workerknoten verwendet.
   > ```json
   > Scale:
   >    Workers:  `2`
   > ```
- Es wird garantiert, dass 1 CPU/virtueller Kern und 512 MB RAM pro Knoten verwendet werden. Es werden mehr als 4 CPUs/virtuelle Kerne und 1024 MB Arbeitsspeicher verwendet:
   > ```json
   > Scheduling:
   >    Default: 
   >      Resources:
   >        Limits:
   >          Cpu:     4
   >          Memory:  1024Mi
   >        Requests:
   >          Cpu:     1
   >          Memory:  512Mi
   > ```
 - Steht für Abfragen zur Verfügung und weist kein Problem auf. Alle Knoten sind aktiv und werden ausgeführt:
   > ```json
   > Status:
   >  ...
   >  Ready Pods:         3/3
   >  State:              Ready
   > ```

**Mit azdata:**

Das allgemeine Format des Befehls lautet:

```console
azdata arc postgres server show -n <server group name>
```

Zum Beispiel:

```console
azdata arc postgres server show -n postgres02
```

Gibt die folgende Ausgabe in einem Format und mit Inhalt zurück, der der von kubectl zurückgegebenen Ausgabe sehr ähnlich ist.

```console
{
  "apiVersion": "arcdata.microsoft.com/v1alpha1",
  "kind": "postgresql-12",
  "metadata": {
    "creationTimestamp": "2020-08-31T21:01:07Z",
    "generation": 10,
    "name": "postgres02",
    "namespace": "arc",
    "resourceVersion": "569516",
    "selfLink": "/apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02",
    "uid": "8a9cd118-361b-4a2e-8a9d-5f9257bf6abb"
  },
  "spec": {
    "engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_stat_statements"
        }
      ]
    },
    "scale": {
      "workers": 2
    },
    "scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "1",
            "memory": "512Mi"
          }
        }
      }
    },
    "service": {
      "type": "NodePort"
    },
    "storage": {
      "data": {
        "className": "local-storage",
        "size": "5Gi"
      },
      "logs": {
        "className": "local-storage",
        "size": "5Gi"
      }
    }
  },
  "status": {
    "externalEndpoint": "10.0.0.4:31066",
    "readyPods": "3/3",
    "state": "Ready"
  }
}
```

## <a name="next-steps"></a>Nächste Schritte
- [Informationen zu den Konzepten von Azure Arc-fähigem PostgreSQL Hyperscale](concepts-distributed-postgres-hyperscale.md)
- [Informationen zum horizontalen Hochskalieren einer Servergruppe (Hinzufügen von Workerknoten)](scale-out-postgresql-hyperscale-server-group.md)
- [Informationen zum zentralen Hoch- oder Herunterskalieren einer Servergruppe (Erhöhen oder Verringern des Arbeitsspeichers und/oder der virtuellen Kerne)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- [Informationen zur Speicherkonfiguration](storage-configuration.md)
- [Informationen zum Überwachen einer Datenbankinstanz](monitor-grafana-kibana.md)
- [Verwenden von PostgreSQL-Erweiterungen in Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppen](using-extensions-in-postgresql-hyperscale-server-group.md)
- [Konfigurieren der Sicherheit für eine Azure Arc-fähige PostgreSQL Hyperscale-Servergruppe](configure-security-postgres-hyperscale.md)
