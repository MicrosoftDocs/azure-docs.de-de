---
title: Verwenden von kubectl zum Bereitstellen einer zustandsbehafteten Kubernetes-App über eine statisch bereitgestellte Freigabe auf einem Azure Stack Edge Pro-Gerät | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie die Bereitstellung einer zustandsbehafteten Kubernetes-Anwendung über eine statisch bereitgestellte Freigabe mithilfe von kubectl auf einem Azure Stack Edge Pro-GPU-Gerät erstellen und verwalten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: c2a14c12baac29d73754bb17e3ca386cc48e1ba0
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96449225"
---
# <a name="use-kubectl-to-run-a-kubernetes-stateful-application-with-a-persistentvolume-on-your-azure-stack-edge-pro-device"></a>Verwenden von kubectl zum Ausführen einer zustandsbehafteten Kubernetes-Anwendung mit PersistentVolume auf einem Azure Stack Edge Pro-Gerät

In diesem Artikel erfahren Sie, wie Sie eine zustandsbehaftete Einzelinstanzanwendung in Kubernetes mit einem PersistentVolume (PV) und einer Bereitstellung bereitstellen. Die Bereitstellung wendet `kubectl`-Befehle auf einen vorhandenen Kubernetes-Cluster an und stellt die Anwendung MySQL bereit. 

Dieses Verfahren ist für Benutzer vorgesehen, die den Artikel [Kubernetes-Speicher auf dem Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-kubernetes-storage.md) gelesen haben und mit den [Kubernetes-Speicherkonzepten](https://kubernetes.io/docs/concepts/storage/) vertraut sind.

Azure Stack Edge Pro unterstützt auch das Ausführen von Azure SQL Edge-Containern, die auf ähnliche Weise bereitgestellt werden, wie hier für MySQL beschrieben. Weitere Informationen finden Sie unter [Azure SQL Edge](../azure-sql-edge/overview.md).


## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie vor der Bereitstellung der zustandsbehafteten Anwendung sicher, dass die folgenden Voraussetzungen auf Ihrem Gerät und dem Client erfüllt sind, den Sie für den Zugriff auf das Gerät verwenden:

### <a name="for-device"></a>Für das Gerät

- Sie verfügen über Anmeldeinformationen für ein Azure Stack Edge Pro-Gerät mit einem Knoten.
    - Das Gerät ist aktiviert. Weitere Informationen finden Sie unter [Aktivieren des Geräts](azure-stack-edge-gpu-deploy-activate.md).
    - Auf dem Gerät wurde die Computerolle über das Azure-Portal konfiguriert, und es verfügt über einen Kubernetes-Cluster. Weitere Informationen finden Sie unter [Konfigurieren von Compute](azure-stack-edge-gpu-deploy-configure-compute.md).

### <a name="for-client-accessing-the-device"></a>Für den Client, der auf das Gerät zugreift

- Sie verfügen über ein Windows-Clientsystem, das für den Zugriff auf das Azure Stack Edge Pro-Gerät verwendet wird.
    - Auf dem Client wird Windows PowerShell 5.0 oder höher ausgeführt. Informationen zum Herunterladen der neuesten Version von Windows PowerShell finden Sie unter [Installieren von Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-7).
    
    - Sie können auch einen anderen Client mit einem [unterstützten Betriebssystem](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) verwenden. In diesem Artikel wird die Vorgehensweise bei Verwendung eines Windows-Clients beschrieben. 
    
    - Sie haben die unter [Zugreifen auf den Kubernetes-Cluster auf dem Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-create-kubernetes-cluster.md) beschriebenen Schritte ausgeführt. Sie haben:
      - den Namespace `userns1` über den Befehl `New-HcsKubernetesNamespace` erstellt. 
      - den Benutzer `user1` über den Befehl `New-HcsKubernetesUser` erstellt. 
      - `user1` Zugriff auf `userns1` über den Befehl `Grant-HcsKubernetesNamespaceAccess` gewährt.       
      - `kubectl` auf dem Client installiert und die Datei `kubeconfig` mit der Benutzerkonfiguration in „C:\\Users\\&lt;Benutzername&gt;\\.kube“ gespeichert. 
    
    - Stellen Sie sicher, dass die `kubectl`-Clientversion um nicht mehr als eine Version von der Kubernetes-Masterversion abweicht, die auf dem Azure Stack Edge Pro-Gerät ausgeführt wird. 
        - Verwenden Sie `kubectl version`, um die kubectl-Version zu überprüfen, die auf dem Client ausgeführt wird. Notieren Sie sich den gesamten Versionsnamen.
        - Wechseln Sie auf der lokalen Benutzeroberfläche des Azure Stack Edge Pro-Geräts zu **Übersicht**, und notieren Sie sich die Kubernetes-Softwarenummer. 
        - Überprüfen Sie anhand der Zuordnungen in der Liste der unterstützten Kubernetes-Versionen, ob diese beiden Versionen kompatibel sind. <!-- insert link-->. 


Sie können nun eine zustandsbehaftete Anwendung auf dem Azure Stack Edge Pro-Gerät bereitstellen. 

## <a name="provision-a-static-pv"></a>Bereitstellen eines statischen PV

Zum statischen Bereitstellen eines PV müssen Sie auf Ihrem Gerät eine Freigabe erstellen. Führen Sie die folgenden Schritte aus, um ein PV für Ihre SMB-Freigabe bereitzustellen. 

> [!NOTE]
> Das in diesem Anleitungsartikel verwendete Beispiel funktioniert nicht bei Verwendung von NFS-Freigaben. Im Allgemeinen können NFS-Freigaben auf Ihrem Azure Stack Edge-Gerät mit Nicht-Datenbankanwendungen bereitgestellt werden.

1. Wählen Sie aus, ob Sie eine Edge-Freigabe oder lokale Edge-Freigabe erstellen möchten. Befolgen Sie die Anweisungen unter [Hinzufügen einer Freigabe](azure-stack-edge-manage-shares.md#add-a-share), um eine Freigabe zu erstellen. Aktivieren Sie das Kontrollkästchen **Freigabe mit Edgecomputing verwenden**.

    ![Lokale Edge-Freigabe für PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/edge-local-share-static-provision-1.png)

    1. Wenn Sie sich entscheiden, eine vorhandene Freigabe zu verwenden, müssen Sie die Freigabe nicht neu erstellen, sondern sie einbinden.
    
        Wechseln Sie im Azure-Portal für Ihre Azure Stack Edge-Ressource zu **Freigaben**. Wählen Sie in der Liste der vorhandenen Freigaben die Freigabe aus, die Sie verwenden möchten, und klicken Sie darauf.

        ![Vorhandene lokale Freigabe für PV auswählen](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-1.png)

    1. Wählen Sie **Einbinden** aus, und bestätigen Sie bei Aufforderung die Einbindung.  

        ![Vorhandene lokale Freigabe für PV einbinden](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-2.png)

1. Notieren Sie sich den Namen der Freigabe. Bei der Erstellung dieser Freigabe wird automatisch ein persistentes Volumeobjekt im Kubernetes-Cluster erstellt, das der von Ihnen erstellten SMB-Freigabe entspricht. 

## <a name="deploy-mysql"></a>Bereitstellen von MySQL

Sie führen nun eine zustandsbehaftete Anwendung aus, indem Sie eine Bereitstellung von Kubernetes erstellen und diese über einen PersistentVolumeClaim (PVC) mit dem im vorherigen Schritt erstellten PV verbinden. 

Alle `kubectl`-Befehle, die Sie zum Erstellen und Verwalten zustandsbehafteter Anwendungsbereitstellungen verwenden, müssen den mit der Konfiguration verbundenen Namespace angeben. Um den Namespace in einem kubectl-Befehl anzugeben, verwenden Sie `kubectl <command> -n <your-namespace>`.

1. Rufen Sie eine Liste der Pods ab, die in Ihrem Kubernetes-Cluster in Ihrem Namespace ausgeführt werden. Ein Pod ist ein Anwendungscontainer oder Prozess, der im Kubernetes-Cluster ausgeführt wird.

   ```powershell
   kubectl get pods -n <your-namespace>
   ```
    
   Hier ist ein Beispiel für die Verwendung des Befehls angegeben:
    
   ```powershell
    C:\Users\user>kubectl get pods -n "userns1"
    No resources found in userns1 namespace.    
    C:\Users\user>
   ```
    
   Die Ausgabe muss angeben, dass keine Ressourcen (Pods) gefunden wurden, da in Ihrem Cluster keine Anwendungen ausgeführt werden.

1. Die folgenden YAML-Dateien werden verwendet. Die Datei `mysql-deployment.yml` beschreibt eine Bereitstellung, die MySQL ausführt und auf den PVC verweist. Die Datei definiert eine Volumebereitstellung für `/var/lib/mysql` und erstellt dann einen PVC, der nach einem 20-GB-Volume sucht. 

    Dieser Anspruch wird durch jedes vorhandene PV erfüllt, das bei der Erstellung der Freigabe im vorherigen Schritt statisch bereitgestellt wurde. Auf Ihrem Gerät wird für jede Freigabe ein großes PV mit 32 TB erstellt. Das PV erfüllt die vom PVC gestellten Anforderungen, und der PVC sollte an dieses PV gebunden sein.

    Kopieren Sie die folgende Datei `mysql-deployment.yml`, und speichern Sie diese in einem Ordner auf dem Windows-Client, den Sie für den Zugriff auf das Azure Stack Edge Pro-Gerät verwenden.
    
    ```yml
    apiVersion: v1
    kind: Service
    metadata:
      name: mysql
    spec:
      ports:
      - port: 3306
      selector:
        app: mysql
      clusterIP: None
    ---
    apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
    kind: Deployment
    metadata:
      name: mysql
    spec:
      selector:
        matchLabels:
          app: mysql
      strategy:
        type: Recreate
      template:
        metadata:
          labels:
            app: mysql
        spec:
          containers:
          - image: mysql:5.6
            name: mysql
            env:
              # Use secret in real usage
            - name: MYSQL_ROOT_PASSWORD
              value: password
            ports:
            - containerPort: 3306
              name: mysql
            volumeMounts:
            - name: mysql-persistent-storage
              mountPath: /var/lib/mysql
          volumes:
          - name: mysql-persistent-storage
            persistentVolumeClaim:
              claimName: mysql-pv-claim
    ```
    
2. Kopieren und speichern Sie sie als Datei `mysql-pv.yml` in denselben Ordner, in dem Sie `mysql-deployment.yml` gespeichert haben. Um die SMB-Freigabe zu verwenden, die Sie zuvor mit `kubectl` erstellt haben, legen Sie das Feld `volumeName` im PVC-Objekt auf den Namen der Freigabe fest. 

    > [!NOTE] 
    > Stellen Sie sicher, dass die YAML-Dateien den richtigen Einzug haben. Sie können die Dateien mit [YAML lint](http://www.yamllint.com/) prüfen und dann speichern.
   
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim
    spec:
      volumeName: <smb-share-name-here>
      storageClassName: ""
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
    ```

3. Stellen Sie die Datei `mysql-pv.yaml` bereit.

    `kubectl apply -f <URI path to the mysql-pv.yml file> -n <your-user-namespace>`
    
    Es folgt eine Beispielausgabe der Bereitstellung.

    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-pv.yml" -n userns1
    persistentvolumeclaim/mysql-pv-claim created
    
    C:\Users\user>
    ```
   Notieren Sie sich den Namen des erstellten PVC. Sie werden ihn in einem späteren Schritt verwenden. 

4. Stellen Sie den Inhalt der Datei `mysql-deployment.yml` bereit.

    `kubectl apply -f <URI path to mysql-deployment.yml file> -n <your-user-namespace>`

    Es folgt eine Beispielausgabe der Bereitstellung.
    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-deployment.yml" -n userns1
        service/mysql created
        deployment.apps/mysql created
    ```
    
5. Zeigen Sie Informationen zur Bereitstellung an.

    `kubectl describe deployment <app-label> -n <your-user-namespace>`
    
    ```powershell
    C:\Users\user>kubectl describe deployment mysql -n userns1
    Name:               mysql
    Namespace:          userns1
    CreationTimestamp:  Tue, 18 Aug 2020 09:44:58 -0700
    Labels:             <none>
    Annotations:        deployment.kubernetes.io/revision: 1
                        kubectl.kubernetes.io/last-applied-configuration:
                          {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"mysql","namespace":"userns1"},"spec":{"selector":{"matchL...
    Selector:           app=mysql
    Replicas:           1 desired | 1 updated | 1 total | 1 available | 0 unavailable
    StrategyType:       Recreate
    MinReadySeconds:    0
    Pod Template:
      Labels:  app=mysql
      Containers:
       mysql:
        Image:      mysql:5.6
        Port:       3306/TCP
        Host Port:  0/TCP
        Environment:
          MYSQL_ROOT_PASSWORD:  password
        Mounts:
          /var/lib/mysql from mysql-persistent-storage (rw)
      Volumes:
       mysql-persistent-storage:
        Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
        ClaimName:  mysql-pv-claim
        ReadOnly:   false
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Progressing    True    NewReplicaSetAvailable
      Available      True    MinimumReplicasAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-c85f7f79c (1/1 replicas created)
    Events:
      Type    Reason             Age   From                   Message
      ----    ------             ----  ----                   -------
      Normal  ScalingReplicaSet  10m   deployment-controller  Scaled up replica set mysql-c85f7f79c to 1
    
    C:\Users\user>
    ```
    

6. Listen Sie die von der Bereitstellung erstellten Pods auf.

    `kubectl get pods -l <app=label> -n <your-user-namespace>`

    Hier ist eine Beispielausgabe.

    
    ```powershell
    C:\Users\user>kubectl get pods -l app=mysql -n userns1
    NAME                    READY   STATUS    RESTARTS   AGE
    mysql-c85f7f79c-vzz7j   1/1     Running   1          14m
    
    C:\Users\user>
    ```
    
7. Untersuchen Sie den PersistentVolumeClaim.

    `kubectl describe pvc <your-pvc-name>`

    Hier ist eine Beispielausgabe.

    
    ```powershell
    C:\Users\user>kubectl describe pvc mysql-pv-claim -n userns1
    Name:          mysql-pv-claim
    Namespace:     userns1
    StorageClass:
    Status:        Bound
    Volume:        mylocalsmbshare1
    Labels:        <none>
    Annotations:   kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"mysql-pv-claim","namespace":"userns1"},"spec":{"acc...
                   pv.kubernetes.io/bind-completed: yes
    Finalizers:    [kubernetes.io/pvc-protection]
    Capacity:      32Ti
    Access Modes:  RWO,RWX
    VolumeMode:    Filesystem
    Mounted By:    mysql-c85f7f79c-vzz7j
    Events:        <none>
    
    C:\Users\user>
    ```
    

## <a name="verify-mysql-is-running"></a>Überprüfen, ob MySQL ausgeführt wird


Um einen Befehl für einen Container in einem Pod auszuführen, in dem MySQL ausgeführt wird, geben Sie Folgendes ein:

`kubectl exec <your-pod-with-the-app> -i -t -n <your-namespace> -- mysql`

Hier ist eine Beispielausgabe.

```powershell
C:\Users\user>kubectl exec mysql-c85f7f79c-vzz7j -i -t -n userns1 -- mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.6.49 MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

## <a name="delete-a-deployment"></a>Löschen einer Bereitstellung

Um die Bereitstellung zu löschen, löschen Sie die bereitgestellten Objekte nach Namen. Zu diesen Objekten gehören Bereitstellung, Dienst und PVC.
 
```powershell
kubectl delete deployment <deployment-name>,svc <service-name> -n <your-namespace>
kubectl delete pvc <your-pvc-name> -n <your-namespace>
```

Hier ist eine Beispielausgabe, wenn Bereitstellung und Dienst gelöscht werden.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```
Hier sehen Sie eine Beispielausgabe, wenn Sie den PVC löschen.

```powershell
C:\Users\user>kubectl delete pvc mysql-pv-claim -n userns1
persistentvolumeclaim "mysql-pv-claim" deleted
C:\Users\user>
```                                                                                         

Das PV ist nicht mehr an den PVC gebunden, weil der PVC gelöscht wurde. Da das PV bei der Erstellung der Freigabe bereitgestellt wurde, müssen Sie die Freigabe löschen. Folgen Sie diesen Schritten:

1. Heben Sie die Bereitstellung der Freigabe auf. Navigieren Sie in Azure-Portal zu **Azure Stack Edge-Ressourcen > Freigaben**, und wählen Sie die Freigabe aus, deren Bereitstellung Sie aufheben möchten. Wählen Sie **Bereitstellung aufheben** aus, und bestätigen Sie den Vorgang. Warten Sie, bis die Bereitstellung der Freigabe aufgehoben wurde. Durch das Aufheben der Bereitstellung wird die Freigabe (und damit das zugehörige PersistentVolume) im Kubernetes-Cluster freigegeben. 

    ![Bereitstellung einer lokalen Freigabe für PV aufheben](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/unmount-edge-local-share-1.png)

1. Sie können jetzt **Löschen** auswählen und den Löschvorgang bestätigen, um die Freigabe zu löschen. Dadurch sollten auch die Freigabe und das entsprechende PV gelöscht werden.

    ![Lokale Freigabe für PV löschen](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/delete-edge-local-share-1.png)


## <a name="next-steps"></a>Nächste Schritte

Lesen Sie den Artikel [Verwenden von kubectl zum Ausführen einer zustandsbehafteten Kubernetes-Anwendung mit StorageClass auf einem Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md), um zu verstehen, wie Speicher dynamisch bereitgestellt wird.