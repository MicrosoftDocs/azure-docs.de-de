---
title: 'Application Gateway-Eingangscontroller: Problembehandlung'
description: Dieser Artikel enthält Dokumentation zur Behandlung häufig auftretender Fragen und/oder Probleme mit dem Application Gateway-Eingangscontroller.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/18/2020
ms.author: caya
ms.openlocfilehash: f2b9f79f0914e645c736f8a577c46baa42587332
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874609"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>Behandeln häufig auftretender Fragen oder Probleme mit dem Eingangscontroller

[Azure Cloud Shell](https://shell.azure.com/) ist die einfachste Methode, um Probleme mit AKS und der AGIC-Installation zu beheben. Starten Sie Ihre Shell über [shell.azure.com](https://shell.azure.com/) oder durch Klicken auf den Link:

[![Start einbetten](https://shell.azure.com/images/launchcloudshell.png "Starten von Azure Cloud Shell")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>Testen mit einer einfachen Kubernetes-App

Für die unten aufgeführten Schritte wird Folgendes vorausgesetzt:
  - Sie verfügen über einen AKS-Cluster, für den erweiterte Netzwerke aktiviert sind.
  - AGIC wurde auf dem AKS-Cluster installiert.
  - Sie verfügen bereits über ein Application Gateway in einem VNET, das für Ihren AKS-Cluster freigegeben ist.

Um sicherzustellen, dass die Application Gateway-, AKS- und AGIC-Installation ordnungsgemäß eingerichtet ist, stellen Sie die einfachstmögliche App bereit:

```bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: test-agic-app-pod
  labels:
    app: test-agic-app
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP
---
apiVersion: v1
kind: Service
metadata:
  name: test-agic-app-service
spec:
  selector:
    app: test-agic-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-agic-app-ingress
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: test.agic.contoso.com
      http:
        paths:
          - path: /
            backend:
              serviceName: test-agic-app-service
              servicePort: 80
EOF
```

Kopieren Sie alle Zeilen gleichzeitig aus dem obigen Skript in eine [Azure Cloud Shell](https://shell.azure.com/), und fügen Sie sie ein. Stellen Sie sicher, dass der gesamte Befehl kopiert wird, beginnend mit `cat` und einschließlich des letzten `EOF`.

![apply](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

Nachdem eine erfolgreiche Bereitstellung der App erfolgt ist, verfügt Ihr AKS-Cluster über einen neuen Pod, einen Dienst und eingehenden Datenverkehr.

Rufen Sie die Liste der Pods mit [Cloud Shell](https://shell.azure.com/) ab: `kubectl get pods -o wide`.
Wir erwarten, dass ein Pod mit dem Namen „test-agic-app-pod“ erstellt wurde. Dieser verfügt über eine IP-Adresse. Diese Adresse muss sich innerhalb des VNETs des Application Gateways befinden, das mit AKS verwendet wird.

![Screenshot des Bash-Fensters in Azure Cloud Shell, das eine Liste von Pods anzeigt, die „test-agic-app-pod“ enthält.](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

Rufen Sie die Liste der Dienste ab: `kubectl get services -o wide`. Wir erwarten, dass ein Dienst mit dem Namen „test-agic-app-service“ angezeigt wird.

![Screenshot des Bash-Fensters in Azure Cloud Shell, das eine Liste von Diensten anzeigt, die „test-agic-app-service“ enthält.](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

Rufen Sie die Liste des eingehenden Datenverkehrs ab: `kubectl get ingress`. Wir gehen davon aus, dass eine Eingangsressource mit dem Namen „test-agic-app-ingress“ erstellt wurde. Die Ressource weist den Hostnamen „test.agic.contoso.com“ auf.

![Screenshot des Bash-Fensters in Azure Cloud Shell, das eine Liste von Eingängen anzeigt, die „test-agic-app-ingress“ enthält.](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

Einer der Pods ist AGIC. `kubectl get pods` zeigt eine Liste von Pods an, von denen einer mit „ingress-azure“ beginnt. Rufen Sie alle Protokolle dieses Pods mit `kubectl logs <name-of-ingress-controller-pod>` ab, um zu überprüfen, ob eine erfolgreiche Bereitstellung erfolgt ist. Bei einer erfolgreichen Bereitstellung wurden dem Protokoll die folgenden Zeilen hinzugefügt:
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

Alternativ können wir aus [Cloud Shell](https://shell.azure.com/) nur die Zeilen abrufen, die eine erfolgreiche Application Gateway-Konfiguration mit `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'` anzeigen, wobei `<ingress-azure....>` der genaue Name des AGIC-Pods sein sollte.

Auf Application Gateway wird die folgende Konfiguration angewendet:

- Listener: ![listener](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png)

- Routingregel: ![routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- Back-End-Pool:
  - Der Back-End-Adresspool enthält eine IP-Adresse, die mit der IP-Adresse des zuvor mit `kubectl get pods -o wide`
![backend_pool](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png) beobachteten Pods übereinstimmt.


Schließlich können wir den `cURL`-Befehl in [Cloud Shell](https://shell.azure.com/) verwenden, um eine HTTP-Verbindung mit der neu bereitgestellten App herzustellen:

1. Verwenden Sie `kubectl get ingress`, um die öffentliche IP-Adresse des Application Gateways abzurufen.
2. Verwenden Sie `curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>`

![Screenshot des Bash-Fensters in Azure Cloud Shell, das einen cURL-Befehl anzeigt, mit dem erfolgreich eine HTTP-Verbindung mit der Test-App hergestellt wird.](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

Das Ergebnis `HTTP/1.1 200 OK` gibt an, dass das Application Gateway, AKS und das AGIC-System erwartungsgemäß funktionieren.


## <a name="inspect-kubernetes-installation"></a>Überprüfen der Kubernetes-Installation

### <a name="pods-services-ingress"></a>Pods, Dienste, eingehender Datenverkehr
Der Application Gateway-Eingangscontroller (Application Gateway Ingress Controller, AGIC) überwacht kontinuierlich die folgenden Kubernetes-Ressourcen: [Bereitstellung](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment) oder [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod), [Dienst](https://kubernetes.io/docs/concepts/services-networking/service/), [Eingehender Datenverkehr](https://kubernetes.io/docs/concepts/services-networking/ingress/)


Folgendes muss vorhanden sein, damit AGIC wie erwartet funktioniert:
  1. AKS muss mindestens einen fehlerfreien **Pod** aufweisen.
     Überprüfen Sie dies aus [Cloud Shell](https://shell.azure.com/) mit `kubectl get pods -o wide --show-labels`. Wenn Sie einen Pod mit einer `apsnetapp` verwenden, kann die Ausgabe wie folgt aussehen:
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. Mindestens ein **Dienst**, der über übereinstimmende `selector`-Bezeichnungen auf die Pods oben verweist.
     Überprüfen Sie dies aus [Cloud Shell](https://shell.azure.com/) mit `kubectl get services -o wide`.
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. **Eingehender Datenverkehr**, der mit `kubernetes.io/ingress.class: azure/application-gateway`kommentiert ist und auf den obigen Dienst verweist. Überprüfen Sie dies aus [Cloud Shell](https://shell.azure.com/) mit `kubectl get ingress -o wide --show-labels`.
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. Anzeigen von Anmerkungen des obigen eingehenden Datenverkehrs: `kubectl get ingress aspnetapp -o yaml` (ersetzen Sie `aspnetapp` durch den Namen Ihres eingehenden Datenverkehrs)
     ```bash
     delyan@Azure:~$ kubectl get ingress aspnetapp -o yaml

     apiVersion: extensions/v1beta1
     kind: Ingress
     metadata:
       annotations:
         kubernetes.io/ingress.class: azure/application-gateway
       name: aspnetapp
     spec:
       backend:
         serviceName: aspnetapp
         servicePort: 80
     ```

     Die Eingangsressource muss mit `kubernetes.io/ingress.class: azure/application-gateway` kommentiert werden.
 

### <a name="verify-observed-namespace"></a>Überprüfen des beobachteten Namespaces

* Rufen Sie die vorhandenen Namespaces im Kubernetes-Cluster ab. In welchem Namespace wird Ihre App ausgeführt? Beobachtet AGIC diesen Namespace? Informationen zur ordnungsgemäßen Konfiguration von beobachteten Namespaces finden Sie in der Dokumentation zur [Unterstützung mehrerer Namespaces](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support).

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* Der AGIC-Pod sollte sich im `default`-Namespace befinden (siehe Spalte `NAMESPACE`). Ein fehlerfreier Pod weist `Running` in der Spalte `STATUS` auf. Es muss mindestens ein AGIC-Pod vorhanden sein.

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* Wenn der AGIC-Pod nicht fehlerfrei ist (Spalte `STATUS` aus dem obigen Befehl ist nicht `Running`):
  - Rufen Sie Protokolle zum Verständnis der Gründe ab: `kubectl logs <pod-name>`
  - Für die vorherige Instanz des Pods: `kubectl logs <pod-name> --previous`
  - Beschreiben Sie den Pod, um mehr Kontext zu erhalten: `kubectl describe pod <pod-name>`


* Verfügen Sie über einen Kubernetes-[Dienst](https://kubernetes.io/docs/concepts/services-networking/service/) und [Eingangsressourcen](https://kubernetes.io/docs/concepts/services-networking/ingress/)?
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* Wird der [eingehende Datenverkehr](https://kubernetes.io/docs/concepts/services-networking/ingress/) folgendermaßen kommentiert: `kubernetes.io/ingress.class: azure/application-gateway`? AGIC beobachtet nur die Kubernetes-Eingangsressourcen, die über diese Anmerkung verfügen.
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* AGIC gibt Kubernetes-Ereignisse für bestimmte kritische Fehler aus. Sie können diese anzeigen:
  - In Ihrem Terminal über `kubectl get events --sort-by=.metadata.creationTimestamp`
  - In Ihrem Browser mithilfe der [Kubernetes-Webbenutzeroberfläche (Dashboard)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)


## <a name="logging-levels"></a>Protokolliergrade

AGIC verfügt über drei Protokolliergrade. Grad 1 ist die Standardeinstellung und zeigt eine minimale Anzahl von Protokollzeilen an.
Grad 5 hingegen zeigt alle Protokolle an, einschließlich der bereinigen Inhalte der auf ARM angewendeten Konfiguration.

Die Kubernetes-Community hat neun Protokolliergrade für das [kubectl](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging)-Tool eingerichtet. In diesem Repository verwenden wir drei dieser Grade mit ähnlicher Semantik:


| Ausführlichkeit | BESCHREIBUNG |
|-----------|-------------|
|  1        | Standardprotokolliergrad, zeigt Startdetails, Warnungen und Fehler an. |
|  3        | Erweiterte Informationen zu Ereignissen und Änderungen, Listen der erstellten Objekte. |
|  5        | Protokolliert gemarshallte Objekte, zeigt die auf ARM angewendete bereinige JSON-Konfiguration an. |


Die Ausführlichkeitsgrade sind über die `verbosityLevel`-Variable in der Datei [helm-config.yaml](#sample-helm-config-file) anpassbar. Erhöhen Sie den Ausführlichkeitsgrad auf `5`, um die auf [ARM](../azure-resource-manager/management/overview.md) angewendete JSON-Konfiguration abzurufen:
  - Fügen Sie `verbosityLevel: 5` in einer eigenen Zeile in [helm-config.yaml](#sample-helm-config-file) hinzu, und führen Sie eine erneute Installation aus.
  - Rufen Sie Protokolle mit `kubectl logs <pod-name>` ab.

### <a name="sample-helm-config-file"></a>Helm-Beispielkonfigurationsdatei
```yaml
# This file contains the essential configs for the ingress controller helm chart

# Verbosity level of the App Gateway Ingress Controller
verbosityLevel: 3

################################################################################
# Specify which application gateway the ingress controller will manage
#
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>

    # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
    # This prohibits AGIC from applying config for any host/path.
    # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
    shared: false

################################################################################
# Specify which kubernetes namespace the ingress controller will watch
# Default value is "default"
# Leaving this variable out or setting it to blank or empty string would
# result in Ingress Controller observing all acessible namespaces.
#
# kubernetes:
#   watchNamespace: <namespace>

################################################################################
# Specify the authentication with Azure Resource Manager
#
# Two authentication methods are available:
# - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
armAuth:
    type: aadPodIdentity
    identityResourceID: <identityResourceId>
    identityClientID:  <identityClientId>

## Alternatively you can use Service Principal credentials
# armAuth:
#    type: servicePrincipal
#    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>

################################################################################
# Specify if the cluster is Kubernetes RBAC enabled or not
rbac:
    enabled: false # true/false

# Specify aks cluster related information. THIS IS BEING DEPRECATED.
aksClusterConfiguration:
    apiServerAddress: <aks-api-server-address>
```