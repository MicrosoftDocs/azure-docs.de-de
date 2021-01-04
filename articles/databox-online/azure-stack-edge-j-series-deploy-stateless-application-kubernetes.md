---
title: Bereitstellen einer zustandslosen Kubernetes-Anwendung auf einem Azure Stack Edge Pro-GPU-Gerät mit kubectl | Microsoft-Dokumentation
description: Hier wird die Erstellung und Verwaltung der Bereitstellung einer zustandslosen Kubernetes-Anwendung mit kubectl auf einem Microsoft Azure Stack Edge Pro-Gerät beschrieben.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 6356089daed02270a14903639afee8001153b195
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96447378"
---
# <a name="deploy-a-kubernetes-stateless-application-via-kubectl-on-your-azure-stack-edge-pro-gpu-device"></a>Bereitstellen einer zustandslosen Kubernetes-Anwendung auf einem Azure Stack Edge Pro-GPU-Gerät mit kubectl

In diesem Artikel wird beschrieben, wie Sie eine zustandslose Anwendung mit kubectl-Befehlen auf einem bestehenden Kubernetes-Cluster bereitstellen können. Dieser Artikel führt Sie auch durch den Prozess der Erstellung und Einrichtung von Pods in Ihrer zustandslosen Anwendung.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie einen Kubernetes-Cluster erstellen und das `kubectl`-Befehlszeilentool verwenden können, müssen Sie Folgendes sicherstellen:

- Sie verfügen über Anmeldeinformationen für ein Azure Stack Edge Pro-Gerät mit einem Knoten.

- Windows PowerShell 5.0 oder höher ist auf einem Windows-Clientsystem installiert, um auf das Azure Stack Edge Pro-Gerät zuzugreifen. Sie können auch einen anderen Client mit einem unterstützten Betriebssystem verwenden. In diesem Artikel wird die Vorgehensweise bei Verwendung eines Windows-Clients beschrieben. Informationen zum Herunterladen der neuesten Version von Windows PowerShell finden Sie unter [Installieren von Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-7).

- Compute ist auf dem Azure Stack Edge Pro-Gerät aktiviert. Wechseln Sie zur Seite **Compute** der lokalen Benutzeroberfläche des Geräts, um den Computevorgang zu aktivieren. Wählen Sie dann eine Netzwerkschnittstelle aus, die Sie für Compute aktivieren möchten. Wählen Sie **Aktivieren** aus. Das Aktivieren des Computevorgangs führt zur Erstellung eines virtuellen Switches auf Ihrem Gerät für diese Netzwerkschnittstelle. Weitere Informationen finden Sie unter [Aktivieren des Computingnetzwerks auf Ihrem Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

- Auf Ihrem Azure Stack Edge Pro-Gerät wird ein Kubernetes-Clusterserver mit Version v1.9 oder höher ausgeführt. Weitere Informationen finden Sie unter [Erstellen und Verwalten eines Kubernetes-Clusters auf einem Microsoft Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-create-kubernetes-cluster.md).

- Sie haben `kubectl` installiert.

## <a name="deploy-a-stateless-application"></a>Bereitstellen einer zustandslosen Anwendung

Bevor wir beginnen, sollten Sie Folgendes ausgeführt haben:

1. Einen Kubernetes-Cluster erstellt.
2. Einen Namespace eingerichtet.
3. Dem Namespace einen Benutzer zugeordnet.
4. Die Benutzerkonfiguration in `C:\Users\<username>\.kube` gespeichert.
5. `kubectl` installiert.

Jetzt können Sie mit der Ausführung und Verwaltung zustandsloser Anwendungsbereitstellungen auf einem Azure Stack Edge Pro-Gerät beginnen. Bevor Sie mit der Verwendung von `kubectl` beginnen, müssen Sie überprüfen, ob Sie die richtige Version von `kubectl` verwenden.

### <a name="verify-you-have-the-correct-version-of-kubectl-and-set-up-configuration"></a>Überprüfen Sie, ob Sie die richtige Version von kubectl besitzen, und richten Sie die Konfiguration ein.

So prüfen Sie die Version von `kubectl`

1. Überprüfen Sie, ob die Version von `kubectl` größer als oder gleich 1.9 ist:

   ```powershell
   kubectl version
   ```
    
   Beispiel für die Ausgabe:
    
   ```powershell
   PS C:\WINDOWS\system32> C:\windows\system32\kubectl.exe version
   Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
   Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
   ```

   In diesem Fall entspricht die Clientversion von kubectl v1.15.2, die kompatibel ist, um den Vorgang fortzusetzen.

2. Rufen Sie eine Liste der Pods ab, die in Ihrem Kubernetes-Cluster ausgeführt werden. Ein Pod ist ein Anwendungscontainer oder Prozess, der im Kubernetes-Cluster ausgeführt wird.

   ```powershell
   kubectl get pods -n <namespace-string>
   ```
    
   Hier ist ein Beispiel für die Verwendung des Befehls angegeben:
    
   ```powershell
   PS C:\WINDOWS\system32> kubectl get pods -n "test1"
   No resources found.
   PS C:\WINDOWS\system32>
   ```
    
   Die Ausgabe muss angeben, dass keine Ressourcen (Pods) gefunden wurden, da in Ihrem Cluster keine Anwendungen ausgeführt werden.

   Der Befehl füllt die Verzeichnisstruktur von „C:\Benutzer\\&lt;Benutzername&gt;\\.kube\" mit Konfigurationsdateien. Das Befehlszeilentool kubectl verwendet diese Dateien, um zustandslose Anwendungen auf Ihrem Kubernetes-Cluster zu erstellen und zu verwalten.

3. Überprüfen Sie manuell die Verzeichnisstruktur von „C:\Benutzer\\&lt;Benutzername&gt;\\.kube\", um sicherzustellen, dass *kubectl* es mit den folgenden Unterordnern gefüllt hat:

   ```powershell
   PS C:\Users\username> ls .kube
    
    
      Directory: C:\Users\user\.kube
    
   Mode                LastWriteTime         Length Name
   ----                -------------         ------ ----
   d-----         2/18/2020 11:05 AM                cache
   d-----         2/18/2020 11:04 AM                http-cache
   -a----         2/18/2020 10:41 AM           5377 config
   ```

> [!NOTE]
> Geben Sie `kubectl --help` ein, um eine Liste aller kubectl-Befehle anzuzeigen.

### <a name="create-a-stateless-application-using-a-deployment"></a>Erstellen einer zustandslosen Anwendung mithilfe einer Bereitstellung

Nachdem Sie sich vergewissert haben, dass die kubectl-Befehlszeilenversion korrekt ist und über die erforderlichen Konfigurationsdateien verfügt, können Sie eine Bereitstellung der zustandslosen Anwendung erstellen.

Ein Pod ist die grundlegende Ausführungseinheit einer Kubernetes-Anwendung, die kleinste und einfachste Einheit im Kubernetes-Objektmodell, die Sie erstellen oder bereitstellen. Ein Pod kapselt auch Speicherressourcen, eine eindeutige Netzwerk-IP-Adresse und Optionen, die bestimmen, wie der/die Container ausgeführt werden sollen.

Der Typ der zustandslosen Anwendung, die Sie erstellen, ist eine nginx-Webserverbereitstellung.

Alle kubectl-Befehle, die Sie zum Erstellen und Verwalten zustandsloser Anwendungsbereitstellungen verwenden, müssen den mit der Konfiguration verbundenen Namespace angeben. Sie haben den Namespace erstellt, während Sie mit dem Cluster auf dem Azure Stack Edge Pro-Gerät im Tutorial [Erstellen und Verwalten eines Kubernetes-Clusters auf einem Microsoft Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-create-kubernetes-cluster.md) mit `New-HcsKubernetesNamespace` verbunden waren.

Um den Namespace in einem kubectl-Befehl anzugeben, verwenden Sie `kubectl <command> -n <namespace-string>`.

Befolgen Sie diese Schritte, um eine nginx-Bereitstellung zu erstellen:

1. Wenden Sie eine zustandslose Anwendung an, indem Sie ein Kubernetes-Bereitstellungsobjekt erstellen:

   ```powershell
   kubectl apply -f <yaml-file> -n <namespace-string>
   ```

   In diesem Beispiel ist der Pfad zur YAML-Datei der Anwendung eine externe Quelle.

   Hier folgt ein Beispiel für die Verwendung des Befehls und der Ausgabe:

   ```powershell
   PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment.yaml -n "test1"
    
   deployment.apps/nginx-deployment created
   ```

   Alternativ können Sie das folgende Markdown auf Ihrem lokalen Computer speichern und den Pfad und Dateinamen im Parameter *-f* ersetzen. Beispiel: „C:\Kubernetes\deployment.yaml“. Hier folgt die Konfiguration für die Bereitstellung der Anwendung:

   ```markdown
   apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
   kind: Deployment
   metadata:
     name: nginx-deployment
   spec:
     selector:
       matchLabels:
         app: nginx
     replicas: 2 # tells deployment to run 2 pods matching the template
     template:
       metadata:
         labels:
           app: nginx
       spec:
         containers:
         - name: nginx
           image: nginx:1.7.9
           ports:
           - containerPort: 80
   ```

   Dieser Befehl erstellt eine standardmäßige nginx-Bereitstellung mit zwei Pods zur Ausführung Ihrer Anwendung.

2. Rufen Sie die Beschreibung der von Ihnen erstellten Kubernetes-nginx-Bereitstellung ab:

   ```powershell
   kubectl describe deployment nginx-deployment -n <namespace-string>
   ```

   Hier folgt ein Beispiel für die Verwendung des Befehls und der Ausgabe:
    
   ```powershell
   PS C:\Users\user> kubectl describe deployment nginx-deployment -n "test1"
    
   Name:                   nginx-deployment
   Namespace:              test1
   CreationTimestamp:      Tue, 18 Feb 2020 13:35:29 -0800
   Labels:                 <none>
   Annotations:            deployment.kubernetes.io/revision: 1
                           kubectl.kubernetes.io/last-applied-configuration:
                             {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"nginx-deployment","namespace":"test1"},"spec":{"repl...
   Selector:               app=nginx
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   StrategyType:           RollingUpdate
   MinReadySeconds:        0
   RollingUpdateStrategy:  25% max unavailable, 25% max surge
   Pod Template:
      Labels:  app=nginx
      Containers:
       nginx:
        Image:        nginx:1.7.9
        Port:         80/TCP
        Host Port:    0/TCP
        Environment:  <none>
        Mounts:       <none>
      Volumes:        <none>
   Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
   OldReplicaSets:  <none>
   NewReplicaSet:   nginx-deployment-5754944d6c (2/2 replicas created)
   Events:
     Type    Reason             Age    From                   Message
     ----    ------             ----   ----                   -------
     Normal  ScalingReplicaSet  2m22s  deployment-controller  Scaled up replica set nginx-deployment-5754944d6c to 2
   ```

   Wenn Sie sich die Einstellung *Replikate* genau ansehen, werden Sie Folgendes feststellen:
    
   ```powershell
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   ```

   Die Einstellung *Replikate* zeigt an, dass Ihre Bereitstellungsspezifikation zwei Pods erforderte, dass diese Pods erstellt und aktualisiert wurden und dass sie für Sie einsatzbereit sind.

   > [!NOTE]
   > Eine Replikatgruppe ersetzt Pods, die aus einem bestimmten Grund gelöscht oder beendet werden, z. B. bei einem Ausfall des Geräteknotens oder einem störenden Geräteupgrade. Aus diesem Grund empfiehlt es sich, eine Replikatgruppe auch dann zu verwenden, wenn in der Anwendung nur ein einziger Pod erforderlich ist.

3. So listen Sie die Pods in Ihrer Bereitstellung auf

   ```powershell
   kubectl get pods -l app=nginx -n <namespace-string>
   ```
    
   Hier folgt ein Beispiel für die Verwendung des Befehls und der Ausgabe:
    
   ```powershell
   PS C:\Users\user> kubectl get pods -l app=nginx -n "test1"
    
   NAME                                READY   STATUS    RESTARTS   AGE
   nginx-deployment-5754944d6c-7wqjd   1/1     Running   0          3m13s
   nginx-deployment-5754944d6c-nfj2h   1/1     Running   0          3m13s
   ```

   Die Ausgabe überprüft, ob zwei Pods mit eindeutigen Namen vorliegen, auf die wir mit kubectl verweisen können.

4. So zeigen Sie Informationen zu einem einzelnen Pod in der Bereitstellung an

   ```powershell
   kubectl describe pod <podname-string> -n <namespace-string>
   ```

   Hier folgt ein Beispiel für die Verwendung des Befehls und der Ausgabe:

   ```powershell
   PS C:\Users\user> kubectl describe pod "nginx-deployment-5754944d6c-7wqjd" -n "test1"

   Name:           nginx-deployment-5754944d6c-7wqjd
   Namespace:      test1
   Priority:       0
   Node:           k8s-1d9qhq2cl-n1/10.128.46.184
   Start Time:     Tue, 18 Feb 2020 13:35:29 -0800
   Labels:         app=nginx
                   pod-template-hash=5754944d6c
   Annotations:    <none>
   Status:         Running
   IP:             172.17.246.200
   Controlled By:  ReplicaSet/nginx-deployment-5754944d6c
    Containers:
      nginx:
        Container ID:   docker://280b0f76bfdc14cde481dc4f2b8180cf5fbfc90a084042f679d499f863c66979
        Image:          nginx:1.7.9
        Image ID:       docker-pullable://nginx@sha256:e3456c851a152494c3e4ff5fcc26f240206abac0c9d794affb40e0714846c451
        Port:           80/TCP
        Host Port:      0/TCP
        State:          Running
          Started:      Tue, 18 Feb 2020 13:35:35 -0800
        Ready:          True
        Restart Count:  0
        Environment:    <none>
        Mounts:
          /var/run/secrets/kubernetes.io/serviceaccount from default-token-8gksw (ro)
    Conditions:
      Type              Status
      Initialized       True
      Ready             True
      ContainersReady   True
      PodScheduled      True
    Volumes:
      default-token-8gksw:
        Type:        Secret (a volume populated by a Secret)
        SecretName:  default-token-8gksw
        Optional:    false
    QoS Class:       BestEffort
    Node-Selectors:  <none>
    Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                     node.kubernetes.io/unreachable:NoExecute for 300s
    Events:
      Type    Reason     Age    From                       Message
      ----    ------     ----   ----                       -------
      Normal  Scheduled  4m58s  default-scheduler          Successfully assigned test1/nginx-deployment-5754944d6c-7wqjd to k8s-1d9qhq2cl-n1
      Normal  Pulling    4m57s  kubelet, k8s-1d9qhq2cl-n1  Pulling image "nginx:1.7.9"
      Normal  Pulled     4m52s  kubelet, k8s-1d9qhq2cl-n1  Successfully pulled image "nginx:1.7.9"
      Normal  Created    4m52s  kubelet, k8s-1d9qhq2cl-n1  Created container nginx
      Normal  Started    4m52s  kubelet, k8s-1d9qhq2cl-n1  Started container nginx
   ```

### <a name="rescale-the-application-deployment-by-increasing-the-replica-count"></a>Neuskalieren der Anwendungsbereitstellung durch Erhöhen der Replikatanzahl

Jeder Pods ist dazu bestimmt, eine einzelne Instanz einer bestimmten Anwendung auszuführen. Wenn Sie die Anwendung horizontal skalieren möchten, um mehrere Instanzen auszuführen, können Sie die Anzahl der Pods erhöhen, d. h. einen für jede Instanz. In Kubernetes wird dies als Replikation bezeichnet.
Sie können die Anzahl der Pods in Ihrer Anwendungsbereitstellung erhöhen, indem Sie eine neue YAML-Datei anwenden. Die YAML-Datei ändert die Einstellung der Replikate auf 4, wodurch sich die Anzahl der Pods in Ihrer Bereitstellung auf vier Pods erhöht. So erhöhen Sie die Anzahl von Pods von 2 auf 4

```powershell
PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment-scale.yaml -n "test1"
```

Alternativ können Sie das folgende Markdown auf Ihrem lokalen Computer speichern und den Pfad und Dateinamen für den Parameter *-f* für `kubectl apply` ersetzen. Beispiel: „C:\Kubernetes\deployment-scale.yaml“. Hier folgt die Konfiguration für die Skalierung der Anwendungsbereitstellung:

```markdown
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 4 # Update the replicas from 2 to 4
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.8
        ports:
        - containerPort: 80
```

So überprüfen Sie, ob die Bereitstellung über vier Pods verfügt

```powershell
kubectl get pods -l app=nginx
```

Hier folgt eine Beispielausgabe für eine neu skalierte Bereitstellung von zwei auf vier Pods:

```powershell
PS C:\WINDOWS\system32> kubectl get pods -l app=nginx

NAME                               READY     STATUS    RESTARTS   AGE
nginx-deployment-148880595-4zdqq   1/1       Running   0          25s
nginx-deployment-148880595-6zgi1   1/1       Running   0          25s
nginx-deployment-148880595-fxcez   1/1       Running   0          2m
nginx-deployment-148880595-rwovn   1/1       Running   0          2m
```

Wie Sie in der Ausgabe sehen können, verfügen Sie jetzt über vier Pods in Ihrer Bereitstellung, die Ihre Anwendung ausführen können.

### <a name="delete-a-deployment"></a>Löschen einer Bereitstellung

Um die Bereitstellung einschließlich aller Pods zu löschen, müssen Sie `kubectl delete deployment` ausführen und dabei den Namen der Bereitstellung *nginx-deployment* und den Namespacenamen angeben. So löschen Sie die Bereitstellung

   ```powershell
   kubectl delete deployment nginx-deployment -n <namespace-string>
   ```

Hier ist ein Beispiel für die Verwendung und Ausgabe des Befehls angegeben:

```powershell
PS C:\Users\user> kubectl delete deployment nginx-deployment -n "test1"
deployment.extensions "nginx-deployment" deleted
```

## <a name="next-steps"></a>Nächste Schritte

[Kubernetes-Übersicht](azure-stack-edge-gpu-kubernetes-overview.md)