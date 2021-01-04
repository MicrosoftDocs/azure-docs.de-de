---
title: Bereitstellen eines Azure SQL Edge-Containers in Kubernetes – Azure SQL Edge
description: Erfahren Sie mehr zum Bereitstellen eines Azure SQL Edge-Containers in Kubernetes
keywords: SQL Edge, Container, Kubernetes
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 31a454c93ad5192f387306a8ec557c4e4d3ae991
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395290"
---
# <a name="deploy-an-azure-sql-edge-container-in-kubernetes"></a>Bereitstellen eines Azure SQL Edge-Containers in Kubernetes

Azure SQL Edge kann in einem Kubernetes-Cluster sowohl über eine auf Kubernetes ausgeführte Azure IoT Edge-Instanz als IoT Edge-Modul als auch als eigenständiger Containerpod bereitgestellt werden. Im restlichen Teil dieses Artikels konzentrieren wir uns auf die eigenständige Containerbereitstellung in einem Kubernetes-Cluster. Weitere Informationen zum Bereitstellen von Azure IoT Edge auf Kubernetes finden Sie unter [Deploy Azure IoT Edge on Kubernetes (preview)](https://microsoft.github.io/iotedge-k8s-doc/introduction.html) (Bereitstellen von Azure IOT Edge auf Kubernetes (Vorschau)).

In diesem Tutorial wird veranschaulicht, wie eine hochverfügbare Azure SQL Edge-Instanz in einem Container auf einem Kubernetes-Cluster konfiguriert wird. 

> [!div class="checklist"]
> * Erstellen eines Systemadministratorkennworts
> * Erstellen des Speichers
> * Erstellen der Bereitstellung
> * Herstellen einer Verbindung mit SSMS (SQL Server Management Studio)
> * Überprüfen von Fehlern und der Wiederherstellung

Kubernetes 1.6 und höher unterstützt [Speicherklassen](https://kubernetes.io/docs/concepts/storage/storage-classes/), [PersistentVolumeClaims](https://kubernetes.io/docs/concepts/storage/storage-classes/#persistentvolumeclaims) und den [Volumetyp Azure-Datenträger](https://github.com/kubernetes/examples/tree/master/staging/volumes/azure_disk). Sie können Ihre Azure SQL Edge-Instanzen nativ in Kubernetes erstellen und verwalten. Das in diesem Artikel enthaltene Beispiel veranschaulicht die Erstellung einer [Bereitstellung](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/), um eine Hochverfügbarkeitskonfiguration ähnlich einer Failoverclusterinstanz auf einem freigegebenen Datenträger zu erstellen. In dieser Konfiguration spielt Kubernetes die Rolle des Clusterorchestrators. Wenn bei einer Azure SQL Edge-Instanz in einem Container ein Fehler auftritt, startet der Orchestrator eine neue Instanz des Containers, die demselben persistenten Speicher angefügt wird.

![Azure SQL Edge in einem Kubernetes-Cluster](media/deploy-kubernetes/kubernetes-sql-edge.png)

Im obigen Diagramm ist `azure-sql-edge` ein Container in einem [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/). Kubernetes orchestriert die Ressourcen im Cluster. Eine [Replikatgruppe](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) stellt sicher, dass der Pod nach dem Ausfall eines Knotens automatisch wiederhergestellt wird. Anwendungen stellen eine Verbindung zum Dienst her. In diesem Fall stellt der Dienst einen Lastenausgleich dar, der eine IP-Adresse hostet, die sich nach dem Ausfall von `azure-sql-edge` nicht ändert.

Im folgenden Diagramm ist der `azure-sql-edge`-Container fehlgeschlagen. Als Orchestrator garantiert Kubernetes die richtige Anzahl fehlerfreier Instanzen in der Replikatgruppe und startet gemäß der Konfiguration einen neuen Container. Der Orchestrator startet einen neuen Pod auf demselben Knoten, und `azure-sql-edge` stellt erneut eine Verbindung mit demselben persistenten Speicher her. Der Dienst stellt eine Verbindung zur neu erstellen `azure-sql-edge`-Instanz her.

![Azure SQL Edge in einem Kubernetes-Cluster nach einem Podausfall](media/deploy-kubernetes/kubernetes-sql-edge-after-pod-fail.png)

Im folgenden Diagramm ist der Knoten fehlgeschlagen, der den `azure-sql-edge`-Container hostet. Der Orchestrator startet den neuen Pod auf einem anderen Knoten, und `azure-sql-edge` stellt erneut eine Verbindung mit demselben persistenten Speicher her. Der Dienst stellt eine Verbindung zur neu erstellen `azure-sql-edge`-Instanz her.

![Azure SQL Edge in einem Kubernetes-Cluster nach einem Knotenausfall](media/deploy-kubernetes/kubernetes-sql-edge-after-node-fail.png)

## <a name="prerequisites"></a>Voraussetzungen

* **Kubernetes-Cluster**
   - Für das Tutorial ist ein Kubernetes-Cluster erforderlich. In den Schritten wird [kubectl](https://kubernetes.io/docs/user-guide/kubectl/) zum Verwalten des Clusters verwendet. 

   - Im Rahmen dieses Tutorials verwenden wir Azure Kubernetes Service zum Bereitstellen von Azure SQL Edge. Unter [Bereitstellen eines Azure Kubernetes Service-Clusters (AKS)](../aks/tutorial-kubernetes-deploy-cluster.md) erfahren Sie, wie Sie einen Kubernetes-Cluster mit einem einzelnen Knoten mithilfe von `kubectl` in AKS erstellen und eine Verbindung mit diesem herstellen. 

   >[!NOTE]
   >Ein Kubernetes-Cluster benötigt mehrere Knoten zum Schutz vor Knotenausfällen.

* **Azure-Befehlszeilenschnittstelle**
   - Die Anweisungen in diesem Tutorial wurden für die Azure CLI 2.10.1 überprüft.

## <a name="create-a-kubernetes-namespace-for-sql-edge-deployment"></a>Erstellen eines Kubernetes-Namespace für die SQL Edge-Bereitstellung

Erstellen Sie im Kubernetes-Cluster einen neuen Namespace. Dieser Namespace wird verwendet, um SQL Edge und alle erforderlichen Artefakte bereitzustellen. Weitere Informationen zu Kubernetes-Namespaces finden Sie unter [Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/).

   ```azurecli
   kubectl create namespace <namespace name>
   ```  

## <a name="create-an-sa-password"></a>Erstellen eines Systemadministratorkennworts

Erstellen Sie ein Systemadministratorkennwort im Kubernetes-Cluster. Kubernetes kann vertrauliche Konfigurationsinformationen wie Kennwörter als [Geheimnisse](https://kubernetes.io/docs/concepts/configuration/secret/) verwalten.

Mit dem folgenden Befehl wird ein Kennwort für das Systemadministratorkonto erstellt:

   ```azurecli
   kubectl create secret generic mssql --from-literal=SA_PASSWORD="MyC0m9l&xP@ssw0rd" -n <namespace name>
   ```  

   Ersetzen Sie `MyC0m9l&xP@ssw0rd` durch ein komplexes Kennwort.

## <a name="create-storage"></a>Erstellen des Speichers

Konfigurieren Sie ein [persistentes Volume](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) und einen [Anspruch auf ein persistentes Volume](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistent-volume-claim-protection) im Kubernetes-Cluster. Führen Sie die folgenden Schritte aus: 

1. Erstellen Sie ein Manifest, um die Speicherklasse und den Anspruch auf ein persistentes Volume zu definieren.  Mit dem Manifest werden der Speicheranbieter, die Parameter und die [reclaim policy](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#reclaiming) (Rückforderungsrichtlinie) festgelegt. Der Kubernetes-Cluster verwendet dieses Manifest, um den persistenten Speicher zu erstellen. 

   Im folgenden YAML-Beispiel werden eine Speicherklasse und ein Anspruch auf persistente Volumes definiert. Der Speicherklassenanbieter lautet `azure-disk`, weil sich dieser Kubernetes-Cluster in Azure befindet. Der Speicherkontotyp lautet `Standard_LRS`. Der Anspruch auf persistente Volumes heißt `mssql-data`. Die Metadaten des Anspruchs auf persistente Volumes enthalten eine Anmerkung, durch die sie auf die Speicherklasse zurückgeführt werden können. 

   ```yaml
   kind: StorageClass
   apiVersion: storage.k8s.io/v1
   metadata:
        name: azure-disk
   provisioner: kubernetes.io/azure-disk
   parameters:
     storageaccounttype: Standard_LRS
     kind: managed
   ---
   kind: PersistentVolumeClaim
   apiVersion: v1
   metadata:
     name: mssql-data
     annotations:
       volume.beta.kubernetes.io/storage-class: azure-disk
   spec:
     accessModes:
     - ReadWriteOnce
     resources:
       requests:
         storage: 8Gi
   ```

   Speichern Sie die Datei (z. B. **pvc.yaml** ).

2. Erstellen Sie einen Anspruch auf ein permanentes Volume in Kubernetes.

   ```azurecli
   kubectl apply -f <Path to pvc.yaml file> -n <namespace name>
   ```

   `<Path to pvc.yaml file>` entspricht dem Speicherort, an dem Sie die Datei gespeichert haben.

   Das persistente Volume wird automatisch als Azure-Speicherkonto erstellt und an den Anspruch auf ein persistentes Volume gebunden. 

    ![Screenshot: Befehl für Anspruch auf persistentes Volume](media/deploy-kubernetes/pvc-cmd.png)

3. Überprüfen Sie den Anspruch auf persistente Volumes.

   ```azurecli
   kubectl describe pvc <PersistentVolumeClaim>  -n <name of the namespace>
   ```

   `<PersistentVolumeClaim>` ist der Name des Anspruchs auf persistente Volumes.

   Im vorherigen Schritt wurde der Anspruch auf persistente Volumes `mssql-data` genannt. Führen Sie den folgenden Befehl aus, um die Metadaten über den Anspruch auf permanente Volumes anzuzeigen:

   ```azurecli
   kubectl describe pvc mssql-data  -n <namespace name>
   ```

   Die zurückgegebenen Metadaten enthalten einen Wert namens `Volume`. Dieser Wert wird dem Namen des Blobs zugeordnet.

   ![Screenshot: zurückgegebene Metadaten, einschließlich Volume](media/deploy-kubernetes/describe-volume.png)

4. Überprüfen Sie das persistente Volume.

   ```azurecli
   kubectl describe pv -n <namespace name>
   ```

   `kubectl` gibt Metadaten zum persistenten Volume zurück, die automatisch generiert und an den Anspruch für das persistente Volume gebunden wurden. 

## <a name="create-the-deployment"></a>Erstellen der Bereitstellung

In diesem Beispiel wird der Container, der die Azure SQL Edge-Instanz hostet, als Kubernetes-Bereitstellungsobjekt bezeichnet. Die Bereitstellung erstellt eine Replikatgruppe. Die Replikatgruppe erstellt einen Pod. 

In diesem Schritt erstellen Sie ein Manifest, um den Container zu beschreiben, der auf dem Azure SQL Edge Docker-Image basiert. Das Manifest referenziert den `mssql-data`-Anspruch auf das persistente Volume und das `mssql`-Geheimnis, das Sie bereits auf den Kubernetes-Cluster angewendet haben. Das Manifest beschreibt außerdem einen [Dienst](https://kubernetes.io/docs/concepts/services-networking/service/). Bei diesem Dienst handelt es sich um einen Lastenausgleich. Der Lastenausgleich garantiert, dass die IP-Adresse beibehalten wird, nachdem die Azure SQL Server-Instanz wiederhergestellt wurde. 

1. Erstellen Sie ein Manifest (eine YAML-Datei), um die Bereitstellung zu beschreiben. Im folgenden Beispiel wird eine Bereitstellung beschrieben, die einen Container enthält, der auf dem Azure SQL Edge-Containerimage basiert.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sqledge-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sqledge
  template:
    metadata:
      labels:
        app: sqledge
    spec:
      volumes:
        - name: sqldata
          persistentVolumeClaim:
            claimName: mssql-data
      containers:
        - name: azuresqledge
          image: mcr.microsoft.com/azure-sql-edge:latest
          ports:
            - containerPort: 1433
          volumeMounts:
            - name: sqldata
              mountPath: /var/opt/mssql
          env:
            - name: MSSQL_PID
              value: "Developer"
            - name: ACCEPT_EULA
              value: "Y"
            - name: SA_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mssql
                  key: SA_PASSWORD
            - name: MSSQL_AGENT_ENABLED
              value: "TRUE"
            - name: MSSQL_COLLATION
              value: "SQL_Latin1_General_CP1_CI_AS"
            - name: MSSQL_LCID
              value: "1033"
      terminationGracePeriodSeconds: 30
      securityContext:
        fsGroup: 10001
---
apiVersion: v1
kind: Service
metadata:
  name: sqledge-deployment
spec:
  selector:
    app: sqledge
  ports:
    - protocol: TCP
      port: 1433
      targetPort: 1433
      name: sql
  type: LoadBalancer
```

   Kopieren Sie den obigen Code in eine neue Datei namens `sqldeployment.yaml`. Ändern Sie die folgenden Werte: 

   * MSSQL_PID `value: "Developer"`: Hiermit wird festgelegt, dass der Container die Azure SQL Edge Developer-Edition ausführt. Die Developer-Edition ist nicht für Produktionsdaten lizenziert. Wenn die Bereitstellung für die Produktion vorgesehen ist, legen Sie die Edition auf `Premium` fest. 

      >[!NOTE]
      >Weitere Informationen finden Sie unter [Azure SQL Edge – Preise](https://azure.microsoft.com/pricing/details/sql-edge/).

   * `persistentVolumeClaim`: Dieser Wert erfordert einen Eintrag für `claimName:`, der den verwendeten Namen dem Anspruch für das persistente Volume zuordnet. In diesem Tutorial wird `mssql-data` verwendet. 

   * `name: SA_PASSWORD`: Konfiguriert das Containerimage, um das Systemadministratorkennwort wie in diesem Abschnitt beschrieben festzulegen.

     ```yaml
     valueFrom:
       secretKeyRef:
         name: mssql
         key: SA_PASSWORD 
     ```

     Wenn Kubernetes den Container bereitstellt, verweist auf das Geheimnis namens `mssql`, um den Wert für das Kennwort abzurufen. 

   >[!NOTE]
   >Mithilfe des Diensttyps `LoadBalancer` wird der Remotezugriff auf die Azure SQL Edge-Instanz (über das Internet) über Port 1433 ermöglicht.

   Speichern Sie die Datei (z. B. **sqledgedeploy.yaml** ).

2. Erstellen Sie die Bereitstellung.

   ```azurecli
   kubectl apply -f <Path to sqledgedeploy.yaml file> -n <namespace name>
   ```

   `<Path to sqldeployment.yaml file>` entspricht dem Speicherort, an dem Sie die Datei gespeichert haben.

   ![Screenshot: Bereitstellungsbefehl](media/deploy-kubernetes/deploy-sql-cmd.png)

   Die Bereitstellung und der Dienst werden erstellt. Die Azure SQL Edge-Instanz befindet sich in einem Container, der mit dem persistenten Speicher verbunden ist.

   Geben Sie `kubectl get pod -n <namespace name>` ein, um den Status des Pods anzuzeigen.

   ![Screenshot: Befehl „get pod“](media/deploy-kubernetes/get-sql-pod-cmd.png)

   In der vorangehenden Abbildung weist der Pod den Status `Running` auf. Dieser Status gibt an, dass der Container bereit ist. Dieser Vorgang kann einige Minuten dauern.

   >[!NOTE]
   >Nachdem die Bereitstellung erstellt wurde, kann es einige Minuten dauern, bis der Pod sichtbar ist. Die Verzögerung liegt daran, dass der Cluster das Azure SQL Edge-Containerimage aus dem Docker-Hub abruft. Nachdem das Image zum ersten Mal per Pull abgerufen wurde, können nachfolgende Bereitstellungen schneller sein, wenn die Bereitstellung einen Knoten nutzt, auf dem bereits ein Image zwischengespeichert wurde. 

3. Überprüfen Sie, ob die Dienste ausgeführt werden. Führen Sie den folgenden Befehl aus:

   ```azurecli
   kubectl get services -n <namespace name>
   ```

   Dieser Befehl gibt alle Dienste, die ausgeführt werden, und die internen sowie externen IP-Adressen der Dienste zurück. Beachten Sie die externe IP-Adresse für den `mssql-deployment`-Dienst. Verwenden Sie diese IP-Adresse, um eine Verbindung mit Azure SQL Edge herzustellen. 

   ![Screenshot: Befehl „get service“](media/deploy-kubernetes/get-service-cmd.png)

   Führen Sie den folgenden Befehl aus, um weitere Informationen zum Status der Objekte im Kubernetes-Cluster zu erhalten:

   ```azurecli
   az aks browse --resource-group <MyResourceGroup> --name <MyKubernetesClustername>
   ```  

## <a name="connect-to-the-azure-sql-edge-instance"></a>Sie stellen eine Verbindung mit der Azure SQL Edge-Instanz her.

Wenn Sie den Container gemäß der Beschreibung konfiguriert haben, können Sie eine Verbindung mit einer Anwendung außerhalb des virtuellen Azure-Netzwerks herstellen. Verwenden Sie das `sa`-Konto und die externe IP-Adresse für den Dienst. Verwenden Sie das Kennwort, das Sie als Kubernetes-Geheimnis konfiguriert haben. Weitere Informationen über das Herstellen einer Verbindung mit einer Azure SQL Edge-Instanz finden Sie unter [Herstellen einer Verbindung mit und Abfragen von Azure SQL Edge (Vorschau)](connect.md).

## <a name="verify-failure-and-recovery"></a>Überprüfen von Fehlern und der Wiederherstellung

Sie können den Pod löschen, um die Fehler und Wiederherstellung zu überprüfen. Führen Sie die folgenden Schritte aus:

1. Listen Sie den Pod auf, auf dem Azure SQL Edge ausgeführt wird.

   ```azurecli
   kubectl get pods -n <namespace name>
   ```

   Notieren Sie sich den Namen des Pods, der Azure SQL Edge ausführt.

2. Löschen Sie den Pod.

   ```azurecli
   kubectl delete pod sqledge-deployment-7df66c9999-rc9xl
   ```
   `sqledge-deployment-7df66c9999-rc9xl` ist der Wert, der im vorherigen Schritt als Name des Pods zurückgegeben wurde. 

Kubernetes erstellt den Pod automatisch neu, um die Azure SQL Edge-Instanz wiederherzustellen und eine Verbindung mit dem beständigen Speicher herzustellen. Verwenden Sie `kubectl get pods`, um zu überprüfen, dass ein neuer Pod bereitgestellt wurde. Verwenden Sie `kubectl get services`, um zu überprüfen, ob die IP-Adresse für den neuen Container gleich ist. 

## <a name="summary"></a>Zusammenfassung

In diesem Tutorial haben Sie gelernt, wie Sie Azure SQL Edge-Container in einem Kubernetes-Cluster für Hochverfügbarkeit bereitstellen. 

> [!div class="checklist"]
> * Erstellen eines Systemadministratorkennworts
> * Erstellen des Speichers
> * Erstellen der Bereitstellung
> * Herstellen einer Verbindung mit Azure SQL Edge Management Studios (SSMS)
> * Überprüfen von Fehlern und der Wiederherstellung

## <a name="next-steps"></a>Nächste Schritte

- [Einführung in Kubernetes](../aks/intro-kubernetes.md)
- [Machine Learning und künstliche Intelligenz mit ONNX in SQL Edge](onnx-overview.md)
- [Entwickeln einer End-to-End-IoT-Lösung mit SQL Edge unter Verwendung von IoT Edge](tutorial-deploy-azure-resources.md)
- [Datenstreaming in Azure SQL Edge](stream-data.md)