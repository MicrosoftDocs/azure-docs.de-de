---
title: Verwenden eines Containers für maschinelles Sehen mit Kubernetes und Helm
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie den Container für maschinelles Sehen mithilfe von Kubernetes und Helm bereitstellen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: aahi
ms.openlocfilehash: d79c52c05d09eedab2dd964acb544c9cdb405380
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562598"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Verwenden eines Containers für maschinelles Sehen mit Kubernetes und Helm

Eine Möglichkeit zur lokalen Verwaltung Ihrer Container für maschinelles Sehen ist die Verwendung von Kubernetes und Helm. Hier erfahren Sie, wie Sie ein Kubernetes-Paket erstellen und dabei Kubernetes und Helm verwenden, um das Containerimage für maschinelles Sehen zu definieren. Dieses Paket wird dann lokal für einen Kubernetes-Cluster bereitgestellt. Außerdem erfahren Sie, wie Sie die bereitgestellten Dienste testen. Weitere Informationen zum Ausführen von Docker-Containern ohne Kubernetes-Orchestrierung finden Sie unter [Installieren und Ausführen von Containern für maschinelles Sehen](computer-vision-how-to-install-containers.md).

## <a name="prerequisites"></a>Voraussetzungen

Die folgenden Voraussetzungen müssen erfüllt sein, damit Container für maschinelles Sehen lokal verwendet werden können:

| Erforderlich | Zweck |
|----------|---------|
| Azure-Konto | Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto][free-azure-account] erstellen, bevor Sie beginnen. |
| Kubernetes-Befehlszeilenschnittstelle | Mithilfe der [Kubernetes-Befehlszeilenschnittstelle][kubernetes-cli] werden die gemeinsam genutzten Anmeldeinformationen aus der Containerregistrierung verwaltet. Kubernetes wird außerdem vor Helm (Kubernetes-Paket-Manager) benötigt. |
| Helm-Befehlszeilenschnittstelle | Installieren Sie die [Helm-Befehlszeilenschnittstelle][helm-install]. Sie wird zum Installieren eines Helm-Charts (Containerpaketdefinition) verwendet. |
| Ressource für maschinelles Sehen |Um den Container zu verwenden, benötigen Sie Folgendes:<br><br>Eine Azure-Ressource vom Typ **Maschinelles Sehen** sowie den zugehörigen API-Schlüssel und Endpunkt-URI. Beide Werte stehen auf der Übersichts- und auf der Schlüsselseite der Ressource zur Verfügung und werden zum Starten des Containers benötigt.<br><br>**{API_KEY}** : Einer der beiden verfügbaren Ressourcenschlüssel auf der Seite **Schlüssel**<br><br>**{ENDPOINT_URI}** : Der Endpunkt, der auf der Seite **Übersicht** angegeben ist|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Der Hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Containeranforderungen und -empfehlungen

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Herstellen einer Verbindung mit dem Kubernetes-Cluster

Für den Hostcomputer muss ein verfügbarer Kubernetes-Cluster vorhanden sein. Das Konzept der Bereitstellung eines Kubernetes-Clusters für einen Hostcomputer wird im Tutorial [Bereitstellen eines Azure Kubernetes Service-Clusters (AKS)](../../aks/tutorial-kubernetes-deploy-cluster.md) erläutert. Weitere Informationen zu Bereitstellungen finden Sie in der [Kubernetes-Dokumentation](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/).

## <a name="configure-helm-chart-values-for-deployment"></a>Konfigurieren von Helm-Chart-Werten für die Bereitstellung

Erstellen Sie zunächst einen Ordner mit dem Namen *read*. Fügen Sie dann den folgenden YAML-Inhalt in eine neue Datei mit dem Namen `chart.yaml` ein:

```yaml
apiVersion: v2
name: read
version: 1.0.0
description: A Helm chart to deploy the Read OCR container to a Kubernetes cluster
dependencies:
- name: rabbitmq
  condition: read.image.args.rabbitmq.enabled
  version: ^6.12.0
  repository: https://kubernetes-charts.storage.googleapis.com/
- name: redis
  condition: read.image.args.redis.enabled
  version: ^6.0.0
  repository: https://kubernetes-charts.storage.googleapis.com/
```

Kopieren Sie zum Konfigurieren der Standardwerte des Helm-Charts den folgenden YAML-Code, und fügen Sie ihn in eine Datei namens `values.yaml` ein. Ersetzen Sie die Kommentare `# {ENDPOINT_URI}` und `# {API_KEY}` durch Ihre eigenen Werte. Konfigurieren Sie bei Bedarf resultExpirationPeriod, Redis und RabbitMQ.

```yaml
# These settings are deployment specific and users can provide customizations
read:
  enabled: true
  image:
    name: cognitive-services-read
    registry:  mcr.microsoft.com/
    repository: azure-cognitive-services/vision/read
    tag: 3.2-preview.1
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
      
      # Result expiration period setting. Specify when the system should clean up recognition results.
      # For example, resultExpirationPeriod=1, the system will clear the recognition result 1hr after the process.
      # resultExpirationPeriod=0, the system will clear the recognition result after result retrieval.
      resultExpirationPeriod: 1
      
      # Redis storage, if configured, will be used by read container to store result records.
      # A cache is required if multiple read containers are placed behind load balancer.
      redis:
        enabled: false # {true/false}
        password: password

      # RabbitMQ is used for dispatching tasks. This can be useful when multiple read containers are
      # placed behind load balancer.
      rabbitmq:
        enabled: false # {true/false}
        rabbitmq:
          username: user
          password: password
```

> [!IMPORTANT]
> - Ohne Angabe der Werte `billing` und `apikey` laufen die Dienste nach 15 Minuten ab. Ebenso führt die Überprüfung zu Fehlern, da die Dienste nicht verfügbar sind.
> 
> - Wenn Sie mehrere Lesecontainer hinter einem Lastenausgleich bereitstellen (z. B. unter Docker Compose oder Kubernetes), benötigen Sie einen externen Cache. Da der Verarbeitungscontainer und der GET-Anforderungscontainer möglicherweise nicht identisch sind, werden die Ergebnisse in einem externen Cache gespeichert und dort für die Container freigegeben. Ausführliche Informationen zu Cacheeinstellungen finden Sie unter [Konfigurieren von Docker-Containern für maschinelles Sehen](./computer-vision-resource-container-config.md).
>

Erstellen Sie den Ordner *templates* unter dem Verzeichnis *read*. Kopieren Sie den folgenden YAML-Code, und fügen Sie ihn in eine Datei mit dem Namen `deployment.yaml` ein. Die `deployment.yaml`-Datei dient als Helm-Vorlage.

> Vorlagen generieren Manifestdateien, die Beschreibungen von Ressourcen im YAML-Format darstellen, die von Kubernetes verstanden werden können. [- Leitfaden zur Vorlage für Helm-Charts][chart-template-guide]

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: read
  labels:
    app: read-deployment
spec:
  selector:
    matchLabels:
      app: read-app
  template:
    metadata:
      labels:
        app: read-app       
    spec:
      containers:
      - name: {{.Values.read.image.name}}
        image: {{.Values.read.image.registry}}{{.Values.read.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.read.image.args.eula}}
        - name: billing
          value: {{.Values.read.image.args.billing}}
        - name: apikey
          value: {{.Values.read.image.args.apikey}}
        args:        
        - ReadEngineConfig:ResultExpirationPeriod={{ .Values.read.image.args.resultExpirationPeriod }}
        {{- if .Values.read.image.args.rabbitmq.enabled }}
        - Queue:RabbitMQ:HostName={{ include "rabbitmq.hostname" . }}
        - Queue:RabbitMQ:Username={{ .Values.read.image.args.rabbitmq.rabbitmq.username }}
        - Queue:RabbitMQ:Password={{ .Values.read.image.args.rabbitmq.rabbitmq.password }}
        {{- end }}      
        {{- if .Values.read.image.args.redis.enabled }}
        - Cache:Redis:Configuration={{ include "redis.connStr" . }}
        {{- end }}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}      
--- 
apiVersion: v1
kind: Service
metadata:
  name: read-service
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

Kopieren Sie die folgenden Hilfsfunktionen in `helpers.tpl`, und fügen Sie sie in denselben Ordner *templates* ein. `helpers.tpl` definiert nützliche Funktionen zum Generieren der Helm-Vorlage.

> [!NOTE]
> Dieser Artikel enthält Verweise auf den Begriff Slave, einen Begriff, den Microsoft nicht mehr verwendet. Sobald der Begriff aus der Software entfernt wird, wird er auch aus diesem Artikel entfernt.

```yaml
{{- define "rabbitmq.hostname" -}}
{{- printf "%s-rabbitmq" .Release.Name -}}
{{- end -}}

{{- define "redis.connStr" -}}
{{- $hostMaster := printf "%s-redis-master:6379" .Release.Name }}
{{- $hostSlave := printf "%s-redis-slave:6379" .Release.Name -}}
{{- $passWord := printf "password=%s" .Values.read.image.args.redis.password -}}
{{- $connTail := "ssl=False,abortConnect=False" -}}
{{- printf "%s,%s,%s,%s" $hostMaster $hostSlave $passWord $connTail -}}
{{- end -}}
```
Die Vorlage gibt einen Lastenausgleichsdienst und die Bereitstellung Ihres Containers/Bilds für das Lesen an.

### <a name="the-kubernetes-package-helm-chart"></a>Das Kubernetes-Paket (Helm-Chart)

Das *Helm-Chart* enthält die Konfiguration, die angibt, welche Docker-Images aus der Containerregistrierung `mcr.microsoft.com` gepullt werden sollen.

> Bei einem [Helm-Chart][helm-charts] handelt es sich um eine Sammlung von Dateien, die eine zusammengehörige Gruppe von Kubernetes-Ressourcen beschreiben. Ein einzelnes Chart kann sowohl für eine einfache Bereitstellung (beispielsweise eines Memcached-Pods) als auch für komplexere Bereitstellungen (etwa eines vollständigen Web-App-Stapels mit HTTP-Servern, Datenbanken, Caches und Ähnlichem) verwendet werden.

Die bereitgestellten *Helm-Charts* pullen die Docker-Images des Diensts für maschinelles Sehen und den entsprechenden Dienst aus der Containerregistrierung `mcr.microsoft.com`.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Installieren des Helm-Charts für den Kubernetes-Cluster

Zum Installieren des *Helm-Charts* muss der Befehl [`helm install`][helm-install-cmd] ausgeführt werden. Stellen Sie sicher, dass Sie den Installationsbefehl im Verzeichnis über dem Ordner `read` ausführen.

```console
helm install read ./read
```

Die Ausgabe nach einer erfolgreichen Installation kann beispielsweise wie folgt aussehen:

```console
NAME: read
LAST DEPLOYED: Thu Sep 04 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                    READY  STATUS             RESTARTS  AGE
read-57cb76bcf7-45sdh   0/1    ContainerCreating  0         0s

==> v1/Service
NAME     TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
read     LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME    READY  UP-TO-DATE  AVAILABLE  AGE
read    0/1    1           0          0s
```

Der Kubernetes-Bereitstellungsvorgang kann mehrere Minuten dauern. Vergewissern Sie sich durch Ausführen des folgenden Befehls, dass die Pods und Dienste ordnungsgemäß bereitgestellt wurden und verfügbar sind:

```console
kubectl get all
```

Die Ausgabe sollte in etwa wie folgt aussehen:

```console
kubectl get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/read-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes     ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/read           LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   1/1     1            1           17s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/read-57cb76bcf7   1         1         1       17s
```

## <a name="deploy-multiple-v3-containers-on-the-kubernetes-cluster"></a>Bereitstellen mehrerer V3-Container auf dem Kubernetes-Cluster

Ab V3 des Containers können Sie die Container parallel auf Aufgaben- und Seitenebene verwenden.

Entwurfsbedingt verfügt jeder V3-Container über einen Verteiler und einen Erkennungsworker. Der Verteiler ist dafür verantwortlich, eine mehrseitige Aufgabe in mehrere einzelne Unteraufgaben mit nur einer Seite aufzuteilen. Der Erkennungsworker ist für die Erkennung eines Dokuments mit nur einer Seite optimiert. Um Parallelität auf Seitenebene zu erreichen, stellen Sie mehrere V3-Container hinter einem Load Balancer bereit, und lassen Sie die Container einen universellen Speicher und eine universelle Warteschlange freigeben. 

> [!NOTE] 
> Zurzeit werden nur Azure Storage und die Azure-Warteschlange unterstützt. 

Der Container, der die Anforderung empfängt, kann die Aufgabe in einseitige untergeordnete Aufgaben aufteilen und sie der universellen Warteschlange hinzufügen. Jeder Erkennungsworker aus einem Container mit geringerer Auslastung kann einseitige Unteraufgaben aus der Warteschlange nutzen, eine Erkennung durchführen und das Ergebnis in den Speicher hochladen. Der Durchsatz kann je nach Anzahl der bereitgestellten Container bis zu `n` Mal verbessert werden.

Kopieren Sie den folgenden YAML-Code, und fügen Sie ihn in eine Datei mit dem Namen `deployment.yaml` ein. Ersetzen Sie die Kommentare `# {ENDPOINT_URI}` und `# {API_KEY}` durch Ihre eigenen Werte. Ersetzen Sie den `# {AZURE_STORAGE_CONNECTION_STRING}`-Kommentar durch Ihre Azure Storage-Verbindungszeichenfolge. Konfigurieren Sie `replicas` auf die gewünschte Anzahl, die im folgenden Beispiel auf `3` festgelegt ist.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: read
  labels:
    app: read-deployment
spec:
  selector:
    matchLabels:
      app: read-app
  replicas: # {NUMBER_OF_READ_CONTAINERS}
  template:
    metadata:
      labels:
        app: read-app
    spec:
      containers:
      - name: cognitive-services-read
        image: mcr.microsoft.com/azure-cognitive-services/vision/read
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: accept
        - name: billing
          value: # {ENDPOINT_URI}
        - name: apikey
          value: # {API_KEY}
        - name: Storage__ObjectStore__AzureBlob__ConnectionString
          value: # {AZURE_STORAGE_CONNECTION_STRING}
        - name: Queue__Azure__ConnectionString
          value: # {AZURE_STORAGE_CONNECTION_STRING}
--- 
apiVersion: v1
kind: Service
metadata:
  name: azure-cognitive-service-read
spec:
  type: LoadBalancer
  ports:
  - port: 5000
    targetPort: 5000
  selector:
    app: read-app
```

Führen Sie den folgenden Befehl aus. 

```console
kubectl apply -f deployment.yaml
```

Nachfolgend finden Sie eine Beispielausgabe, die bei erfolgreicher Bereitstellungsausführung angezeigt werden kann:

```console
deployment.apps/read created
service/azure-cognitive-service-read created
```

Der Kubernetes-Bereitstellungsvorgang kann mehrere Minuten dauern. Vergewissern Sie sich, dass die Pods und Dienste ordnungsgemäß bereitgestellt wurden und verfügbar sind, und führen Sie dann den folgenden Befehl aus:

```console
kubectl get all
```

Eine Konsolenausgabe ähnlich der folgenden sollte angezeigt werden:

```console
kubectl get all
NAME                       READY   STATUS    RESTARTS   AGE
pod/read-6cbbb6678-58s9t   1/1     Running   0          3s
pod/read-6cbbb6678-kz7v4   1/1     Running   0          3s
pod/read-6cbbb6678-s2pct   1/1     Running   0          3s

NAME                                   TYPE           CLUSTER-IP   EXTERNAL-IP    PORT(S)          AGE
service/azure-cognitive-service-read   LoadBalancer   10.0.134.0   <none>         5000:30846/TCP   17h
service/kubernetes                     ClusterIP      10.0.0.1     <none>         443/TCP          78d

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   3/3     3            3           3s

NAME                             DESIRED   CURRENT   READY   AGE
replicaset.apps/read-6cbbb6678   3         3         3       3s
```

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Nächste Schritte

Ausführlichere Informationen zum Installieren von Anwendungen mit Helm in Azure Kubernetes Service (AKS) finden Sie [hier][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Containerunterstützung in Azure Cognitive Services][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md