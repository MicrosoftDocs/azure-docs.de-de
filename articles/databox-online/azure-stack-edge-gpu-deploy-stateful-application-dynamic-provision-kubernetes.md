---
title: Verwenden von kubectl zum Bereitstellen einer zustandsbehafteten Kubernetes-App über eine dynamisch bereitgestellte Freigabe auf einem Azure Stack Edge Pro-GPU-Gerät | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Bereitstellung einer zustandsbehafteten Kubernetes-Anwendung über eine dynamisch bereitgestellte Freigabe mithilfe von kubectl auf einem Microsoft Azure Stack Edge Pro-GPU-Gerät erstellen und verwalten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/26/2020
ms.author: alkohli
ms.openlocfilehash: 81a52b26c5291f788ac81caeb2ca5416a2f58d36
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448880"
---
# <a name="use-kubectl-to-run-a-kubernetes-stateful-application-with-storageclass-on-your-azure-stack-edge-pro-gpu-device"></a>Verwenden von kubectl zum Ausführen einer zustandsbehafteten Kubernetes-Anwendung mit StorageClass auf einem Azure Stack Edge Pro-GPU-Gerät

In diesem Artikel erfahren Sie, wie Sie eine zustandsbehaftete Einzelinstanzanwendung in Kubernetes mithilfe von StorageClass bereitstellen, um Speicher und eine Bereitstellung dynamisch bereitzustellen. Die Bereitstellung wendet `kubectl`-Befehle auf einen vorhandenen Kubernetes-Cluster an und stellt die Anwendung MySQL bereit. 

Dieses Verfahren ist für Benutzer vorgesehen, die mit dem [Kubernetes-Speicher auf Azure Stack Edge Pro-Geräten](azure-stack-edge-gpu-kubernetes-storage.md) und den [Kubernetes-Speicherkonzepten](https://kubernetes.io/docs/concepts/storage/) vertraut sind.


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
        - Überprüfen Sie anhand der Zuordnungen in der Liste der unterstützten Kubernetes-Versionen, ob diese beiden Versionen kompatibel sind.<!-- insert link-->. 


Sie können nun eine zustandsbehaftete Anwendung auf dem Azure Stack Edge Pro-Gerät bereitstellen. 


## <a name="deploy-mysql"></a>Bereitstellen von MySQL

Sie führen nun eine zustandsbehaftete Anwendung aus, indem Sie eine Bereitstellung von Kubernetes erstellen und diese über einen PersistentVolumeClaim (PVC) mit der integrierten StorageClass-Instanz verbinden. 

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

1. Die folgenden YAML-Dateien werden verwendet. Die Datei `mysql-deployment.yml` beschreibt eine Bereitstellung, die MySQL ausführt und auf den PVC verweist. Die Datei definiert eine Volumebereitstellung für `/var/lib/mysql` und erstellt dann einen PVC, der nach einem 20-GB-Volume sucht. Ein dynamisches PersistentVolume (PV) wird bereitgestellt und der PVC an dieses PV gebunden.

    Kopieren und speichern Sie die folgende Datei `mysql-deployment.yml` in einen Ordner auf dem Windows-Client, den Sie für den Zugriff auf das Azure Stack Edge Pro-Gerät verwenden.
    
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
              claimName: mysql-pv-claim-sc
    ```
    
2. Kopieren und speichern Sie sie als Datei `mysql-pvc.yml` in denselben Ordner, in dem Sie `mysql-deployment.yml` gespeichert haben. Um die integrierte StorageClass-Instanz dieses Azure Stack Edge Pro-Geräts auf einem angefügten Datenträger zu verwenden, legen Sie das Feld `storageClassName` im PVC-Objekt auf `ase-node-local` und accessModes auf `ReadWriteOnce` fest. 

    > [!NOTE] 
    > Stellen Sie sicher, dass die YAML-Dateien den richtigen Einzug haben. Sie können die Dateien mit [YAML lint](http://www.yamllint.com/) prüfen und dann speichern.
   
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim-sc
    spec:
      storageClassName: ase-node-local
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
    ```

3. Stellen Sie die Datei `mysql-pvc.yaml` bereit.

    `kubectl apply -f <URI path to the mysql-pv.yml file> -n <your-user-namespace>`
    
    Es folgt eine Beispielausgabe der Bereitstellung.

    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-pvc.yml" -n userns1
    persistentvolumeclaim/mysql-pv-claim-sc created
    C:\Users\user>
    ```
   Notieren Sie sich den Namen des erstellten PVC: `mysql-pv-claim-sc`. Sie werden ihn in einem späteren Schritt verwenden. 

4. Stellen Sie den Inhalt der Datei `mysql-deployment.yml` bereit.

    `kubectl apply -f <URI path to mysql-deployment.yml file> -n <your-user-namespace>`

    Es folgt eine Beispielausgabe der Bereitstellung.
    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-deployment.yml" -n userns1
    service/mysql created
    deployment.apps/mysql created
    C:\Users\user>
    ```
    
5. Zeigen Sie Informationen zur Bereitstellung an.

    `kubectl describe deployment <app-label> -n <your-user-namespace>`
    
    ```powershell
    C:\Users\user>kubectl describe deployment mysql -n userns1
    Name:               mysql
    Namespace:          userns1
    CreationTimestamp:  Thu, 20 Aug 2020 11:14:25 -0700
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
        ClaimName:  mysql-pv-claim-sc
        ReadOnly:   false
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-695c4d9dcd (1/1 replicas created)
    Events:
      Type    Reason             Age   From                   Message
      ----    ------             ----  ----                   -------
      Normal  ScalingReplicaSet  24s   deployment-controller  Scaled up replica set mysql-695c4d9dcd to 1
    C:\Users\user>
    ```
    

6. Listen Sie die von der Bereitstellung erstellten Pods auf.

    `kubectl get pods -l <app=label> -n <your-user-namespace>`

    Hier ist eine Beispielausgabe.

    
    ```powershell
    C:\Users\user>kubectl get pods -l app=mysql -n userns1
    NAME                     READY   STATUS    RESTARTS   AGE
    mysql-695c4d9dcd-rvzff   1/1     Running   0          40s
    C:\Users\user>
    ```
    
7. Untersuchen Sie den PersistentVolumeClaim.

    `kubectl describe pvc <your-pvc-name>`

    Hier ist eine Beispielausgabe.

    
    ```powershell
    C:\Users\user>kubectl describe pvc mysql-pv-claim-sc -n userns1
    Name:          mysql-pv-claim-sc
    Namespace:     userns1
    StorageClass:  ase-node-local
    Status:        Bound
    Volume:        pvc-dc48253c-82dc-42a4-a7c6-aaddc97c9b8a
    Labels:        <none>
    Annotations:   kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"mysql-pv-claim-sc","namespace":"userns1"},"spec":{"...
                   pv.kubernetes.io/bind-completed: yes
                   pv.kubernetes.io/bound-by-controller: yes
                   volume.beta.kubernetes.io/storage-provisioner: rancher.io/local-path
                   volume.kubernetes.io/selected-node: k8s-3q7lhq2cl-3q7lhq2
    Finalizers:    [kubernetes.io/pvc-protection]
    Capacity:      20Gi
    Access Modes:  RWO
    VolumeMode:    Filesystem
    Mounted By:    mysql-695c4d9dcd-rvzff
    Events:
      Type    Reason                 Age                From                                                                                                Message
      ----    ------                 ----               ----                                                                                                -------
      Normal  WaitForFirstConsumer   71s (x2 over 77s)  persistentvolume-controller                                                                         waiting for first consumer to be created before binding
      Normal  ExternalProvisioning   62s                persistentvolume-controller                                                                         waiting for a volume to be created, either by external provisioner "rancher.io/local-path" or manually created by system administrator
      Normal  Provisioning           62s                rancher.io/local-path_local-path-provisioner-6b84988bf9-tx8mz_1896d824-f862-4cbf-912a-c8cc0ca05574  External provisioner is provisioning volume for claim "userns1/mysql-pv-claim-sc"
      Normal  ProvisioningSucceeded  60s                rancher.io/local-path_local-path-provisioner-6b84988bf9-tx8mz_1896d824-f862-4cbf-912a-c8cc0ca05574  Successfully provisioned volume pvc-dc48253c-82dc-42a4-a7c6-aaddc97c9b8a
    C:\Users\user>
    ```
    

## <a name="verify-mysql-is-running"></a>Überprüfen, ob MySQL ausgeführt wird

Um sicherzustellen, dass die Anwendung ausgeführt wird, geben Sie Folgendes ein:

`kubectl exec <your-pod-with-the-app> -i -t -n <your-namespace> -- mysql -p`

Wenn Sie dazu aufgefordert werden, geben Sie das Kennwort ein. Das Kennwort befindet sich in der Datei `mysql-deployment`.

Hier ist eine Beispielausgabe.

```powershell
C:\Users\user>kubectl exec mysql-695c4d9dcd-rvzff -i -t -n userns1 -- mysql -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
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
C:\Users\user>kubectl delete pvc mysql-pv-claim-sc -n userns1
persistentvolumeclaim "mysql-pv-claim-sc" deleted
C:\Users\user>
```                                                                                         


## <a name="next-steps"></a>Nächste Schritte

Informationen zur Netzwerkkonfiguration über kubectl finden Sie unter [Bereitstellen einer zustandslosen Anwendung auf einem Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)