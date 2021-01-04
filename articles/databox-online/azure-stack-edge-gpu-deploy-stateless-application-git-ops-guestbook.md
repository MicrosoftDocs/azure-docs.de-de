---
title: Bereitstellen einer PHP-Gästebuch-App in einer für Arc aktivierten Kubernetes-Implementierung auf einem Azure Stack Edge Pro-GPU-Gerät | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie eine zustandslose PHP-Gästebuchanwendung mit Redis unter Verwendung von GitOps in einem für Arc aktivierten Kubernetes-Cluster Ihres Azure Stack Edge Pro-Geräts bereitstellen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/25/2020
ms.author: alkohli
ms.openlocfilehash: 82b786f3c90a7ef482ccbcd6e66fb9abfc4eea5f
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561935"
---
# <a name="deploy-a-php-guestbook-stateless-application-with-redis-on-arc-enabled-kubernetes-cluster-on-azure-stack-edge-pro-gpu"></a>Bereitstellen einer zustandslosen PHP-Gästebuchanwendung mit Redis in einem für Arc aktivierten Kubernetes-Cluster auf einem Azure Stack Edge Pro-GPU-Gerät

In diesem Artikel wird erläutert, wie Sie eine einfache Multi-Tier-Webanwendung unter Verwendung von Kubernetes und Azure Arc erstellen und bereitstellen. Dieses Beispiel umfasst die folgenden Komponenten:

- Einen Redis-Master (Einzelinstanz) zum Speichern von Gästebucheinträgen
- Mehrere replizierte Redis-Instanzen für Lesevorgänge
- Mehrere Front-End-Webinstanzen

Die Bereitstellung erfolgt unter Verwendung von GitOps in dem für Arc aktivierten Kubernetes-Cluster auf Ihrem Azure Stack Edge Pro-Gerät. 

Dieses Verfahren ist für Benutzer gedacht, die den Artikel [Kubernetes-Workloads auf einem Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-kubernetes-workload-management.md) gelesen haben und mit den Konzepten von [Was ist Kubernetes mit Azure Arc-Unterstützung (Vorschauversion)?](../azure-arc/kubernetes/overview.md) vertraut sind.


## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie vor der Bereitstellung der zustandslosen Anwendung sicher, dass die folgenden Voraussetzungen auf Ihrem Gerät und auf dem Client erfüllt sind, den Sie für den Zugriff auf das Gerät verwenden:

> [!NOTE]
> Dieser Artikel enthält Verweise auf den Begriff Slave, einen Begriff, den Microsoft nicht mehr verwendet. Sobald der Begriff aus der Software entfernt wird, wird er auch aus diesem Artikel entfernt.

### <a name="for-device"></a>Für das Gerät

1. Sie verfügen über Anmeldeinformationen für ein Azure Stack Edge Pro-Gerät mit einem Knoten.
    1. Das Gerät ist aktiviert. Siehe [Aktivieren des Geräts](azure-stack-edge-gpu-deploy-activate.md).
    1. Das Gerät verfügt über die über das Azure-Portal konfigurierte Computerolle und umfasst einen Kubernetes-Cluster. Siehe [Konfigurieren der Computeumgebung](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Sie haben Azure Arc im vorhandenen Kubernetes-Cluster auf Ihrem Gerät aktiviert und verfügen im Azure-Portal über eine entsprechende Azure Arc-Ressource. Die ausführlichen Schritte sind unter [Aktivieren von Azure Arc auf einem Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md) beschrieben.

### <a name="for-client-accessing-the-device"></a>Für den Client, der auf das Gerät zugreift

1. Sie verfügen über ein Windows-Clientsystem, das für den Zugriff auf das Azure Stack Edge Pro-Gerät verwendet wird.
  
    - Auf dem Client wird Windows PowerShell 5.0 oder höher ausgeführt. Informationen zum Herunterladen der neuesten Version von Windows PowerShell finden Sie unter [Installieren von Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-7).
    
    - Sie können auch einen anderen Client mit einem [unterstützten Betriebssystem](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) verwenden. In diesem Artikel wird die Vorgehensweise bei Verwendung eines Windows-Clients beschrieben. 
    
1. Sie haben die unter [Zugreifen auf den Kubernetes-Cluster auf dem Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-create-kubernetes-cluster.md) beschriebenen Schritte ausgeführt. Sie haben:
    
    - `kubectl` auf dem Client installiert.  <!--and saved the `kubeconfig` file with the user configuration to C:\\Users\\&lt;username&gt;\\.kube. -->
    
    - Stellen Sie sicher, dass die `kubectl`-Clientversion um nicht mehr als eine Version von der Kubernetes-Masterversion abweicht, die auf dem Azure Stack Edge Pro-Gerät ausgeführt wird. 
      - Verwenden Sie `kubectl version`, um die kubectl-Version zu überprüfen, die auf dem Client ausgeführt wird. Notieren Sie sich den gesamten Versionsnamen.
      - Navigieren Sie auf der lokalen Benutzeroberfläche des Azure Stack Edge Pro-Geräts zu **Übersicht**, und notieren Sie sich die Kubernetes-Softwarenummer. 
      - Überprüfen Sie anhand der Zuordnungen in der Liste der unterstützten Kubernetes-Versionen, ob diese beiden Versionen kompatibel sind. <!--insert link-->.

1. Sie verfügen über eine [GitOps-Konfiguration, die Sie zum Ausführen einer Azure Arc-Bereitstellung verwenden können](https://github.com/kagoyal/dbehaikudemo). In diesem Beispiel werden die folgenden `yaml`-Dateien für die Bereitstellung auf Ihrem Azure Stack Edge Pro-Gerät verwendet.

    - `frontend-deployment.yaml`<!-- - The guestbook application has a web frontend serving the HTTP requests written in PHP. It is configured to connect to the redis-master Service for write requests and the redis-slave service for Read requests. This file describes a deployment that runs the frontend of the guestbook application.-->
    - `frontend-service.yaml` <!-- - This allows you to configure an externally visible frontend Service that can be accessed from outside the Kubernetes cluster on your device.-->
    - `redis-master-deployment.yaml` <!-- - This deployment file runs a single replica Redis master Pod. The guestbook application uses Redis to store its data. It writes its data to a Redis master instance and reads data from multiple Redis slave instances.-->
    - `redis-master-service.yaml` <!-- - The guestbook application needs to communicate to the Redis master to write its data. You need to apply a Service to proxy the traffic to the Redis master Pod. A Service defines a policy to access the Pods.-->
    - `redis-slave-deployment.yaml` <!-- - Although the Redis master is a single pod, you can make it highly available to meet traffic demands by adding replica Redis slaves. The Redis Slave Deployment specifies two replicas.-->
    - `redis-slave-service.yaml` <!-- - The guestbook application needs to communicate to Redis slaves to read data. To make the Redis slaves discoverable, you need to set up a Service. A Service provides transparent load balancing to a set of Pods.-->

<!-- az cli version requirements-->


## <a name="deploy-configuration"></a>Bereitstellen der Konfiguration

Führen Sie diese Schritte aus, um die Azure Arc-Ressource für die Bereitstellung einer GitOps-Konfiguration über das Azure-Portal zu konfigurieren: 

1. Navigieren Sie im Azure-Portal zur Azure Arc-Ressource, die Sie beim Aktivieren von Azure Arc im Kubernetes-Cluster auf Ihrem Gerät erstellt haben. 

    ![Wechseln zur Azure Arc-Ressource](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. Navigieren Sie zu **Konfigurationen**, und wählen Sie **+ Konfiguration hinzufügen** aus.

    ![Screenshot: Kubernetes-Cluster mit Azure Arc-Unterstützung und ausgewählter Option „Konfiguration hinzufügen“](media/azure-stack-edge-gpu-connect-powershell-interface/select-configurations-1.png)

1. Geben Sie unter **Konfiguration hinzufügen** die geeigneten Werte für die Felder ein, und wählen Sie **Anwenden** aus.

    |Parameter  |BESCHREIBUNG |
    |---------|---------|
    |Konfigurationsname     | Name der Konfigurationsressource.        |
    |Name der Operatorinstanz     |Instanzname des Operators zur Identifizierung einer bestimmten Konfiguration. Dieser Name ist eine Zeichenfolge aus maximal 253 Zeichen, bei denen es sich ausschließlich um Kleinbuchstaben, alphanumerische Zeichen, Bindestriche und Punkte handeln darf.         |
    |Operatornamespace     | Legen Sie diesen Namespace auf **demotestguestbook** fest, da dieser Name dem in der Bereitstellung `yaml` angegebenen Namespace entspricht. <br> Das Feld definiert den Namespace, in dem der Operator installiert ist. Dieser Name ist eine Zeichenfolge aus maximal 253 Zeichen, bei denen es sich ausschließlich um Kleinbuchstaben, alphanumerische Zeichen, Bindestriche und Punkte handeln darf.         |
    |Repository-URL     |<br>Pfad zum Git-Repository im `http://github.com/username/repo`- oder `git://github.com/username/repo`-Format, in dem sich Ihre GitOps-Konfiguration befindet.         |
    |Operatorbereich     | Wählen Sie **Namespace** aus. <br>Über diese Einstellung wird der Bereich definiert, in dem der Operator installiert ist. Wählen Sie „Namespace“ aus. Ihr Operator wird in dem Namespace installiert, der in den YAML-Bereitstellungsdateien angegeben ist.       |
    |Operatortyp     | Übernehmen Sie die Standardeinstellung. <br>Über dieses Feld wird der Typ des Operators angegeben. Standardmäßig ist „flux“ festgelegt.        |
    |Operatorparameter     | Lassen Sie dieses Feld leer. <br>Dieses Feld enthält Parameter zum Übergeben des Flux-Operators.        |
    |Helm     | Legen Sie für dieses Feld **Deaktiviert** fest. <br>Aktivieren Sie diese Option, wenn Sie Chart-basierte Bereitstellungen vornehmen.        |


    ![Hinzufügen einer Konfiguration](media/azure-stack-edge-gpu-connect-powershell-interface/add-configuration-1.png)


1. Die Bereitstellung der Konfiguration wird gestartet, und für **Operatorstatus** wird **Ausstehend** angezeigt. 

    ![Screenshot: Kubernetes-Cluster mit Azure Arc-Unterstützung im Zustand „Ausstehend“ während der Aktualisierung](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-1.png)

1. Die Bereitstellung nimmt einige Minuten in Anspruch. Wenn die Bereitstellung abgeschlossen ist, wird für **Operatorstatus** **Installiert** angezeigt.

    ![Screenshot: Kubernetes-Cluster mit Azure Arc-Unterstützung im Zustand „Installiert“](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-2.png)


## <a name="verify-deployment"></a>Überprüfen der Bereitstellung

Bei der Bereitstellung über die GitOps-Konfiguration wird ein `demotestguestbook`-Namespace gemäß Angabe in den `yaml`-Bereitstellungsdateien im Git-Repository erstellt.

1. Nachdem Sie die GitOps-Konfiguration angewendet haben, führen Sie die Schritte unter [Herstellen einer Verbindung mit der PowerShell-Schnittstelle des Geräts](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) aus.
1. Führen Sie den folgenden Befehl aus, um die ausgeführten Pods im Namespace `demotestguestbook` der Bereitstellung aufzulisten.

    `kubectl get pods -n <your-namespace>`
  
    Nachfolgend ist eine Beispielausgabe gezeigt.
  
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n demotestguestbook
    NAME                            READY   STATUS    RESTARTS   AGE
    aseoperator1-5569658644-cqtb5   1/1     Running   0          91m
    frontend-6cb7f8bd65-4xb4f       1/1     Running   0          91m
    frontend-6cb7f8bd65-q9cxj       1/1     Running   0          91m
    frontend-6cb7f8bd65-xpzs6       1/1     Running   0          91m
    memcached-86bdf9f56b-5l2fq      1/1     Running   0          91m
    redis-master-7db7f6579f-2z29w   1/1     Running   0          91m
    redis-slave-7664787fbc-lgr2n    1/1     Running   0          91m
    redis-slave-7664787fbc-vlvzn    1/1     Running   0          91m
    [10.128.44.240]: PS>
    ```  

1. In diesem Beispiel wurde der Front-End-Dienst als „type:LoadBalancer“ bereitgestellt. Zum Anzeigen des Gästebuchs müssen Sie die IP-Adresse dieses Diensts ermitteln. Führen Sie den folgenden Befehl aus.

    `kubectl get service -n <your-namespace>`
    
    ```powershell
    [10.128.44.240]: PS>kubectl get service -n demotestguestbook
    NAME           TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)        AGE
    frontend       LoadBalancer   10.96.79.38      10.128.44.245   80:31238/TCP   85m
    memcached      ClusterIP      10.102.47.75     <none>          11211/TCP      85m
    redis-master   ClusterIP      10.104.32.99     <none>          6379/TCP       85m
    redis-slave    ClusterIP      10.104.215.146   <none>          6379/TCP       85m
    [10.128.44.240]: PS>
    ```
1. Der Front-End-Dienst von `type:LoadBalancer` verfügt über eine externe IP-Adresse. Diese IP-Adresse stammt aus dem IP-Adressbereich, den Sie beim Konfigurieren der Computenetzwerkeinstellungen auf dem Gerät für externe Dienste angegeben haben. Verwenden Sie diese IP-Adresse, um das Gästebuch unter der folgenden URL anzuzeigen: `https://<external-IP-address>`.

    ![Anzeige des Gästebuchs](media/azure-stack-edge-gpu-connect-powershell-interface/view-guestbook-1.png)

## <a name="delete-deployment"></a>Löschen der Bereitstellung

Um die Bereitstellung zu löschen, können Sie die Konfiguration im Azure-Portal löschen. Dadurch werden die erstellten Objekte gelöscht (einschließlich Bereitstellungen und Dienste).

1. Navigieren Sie im Azure-Portal zur Azure Arc-Ressource und dann zu „Konfigurationen“. 
1. Suchen Sie nach der Konfiguration, die Sie löschen möchten. Wählen Sie die Auslassungspunkte (...) aus, um das Kontextmenü zu öffnen, und wählen Sie dann **Löschen** aus.
    ![Löschen der Konfiguration](media/azure-stack-edge-gpu-connect-powershell-interface/delete-configuration-1.png)

Es kann einige Minuten dauern, bis die Konfiguration gelöscht wurde.
 
<!--```powershell
kubectl delete deployment <deployment-name> -n <your-namespace>
kubectl delete service <service-name> -n <your-namespace>
```

Here is a sample output of when you delete the deployments and the services.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```-->


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie das [Kubernetes-Dashboard zum Überwachen von Bereitstellungen auf Ihrem Azure Stack Edge Pro-Gerät verwenden](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).