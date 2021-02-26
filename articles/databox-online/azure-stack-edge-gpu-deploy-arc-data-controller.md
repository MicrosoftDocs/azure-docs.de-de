---
title: Bereitstellen eines Azure Arc-Datencontrollers auf Ihrem Azure Stack Edge Pro-GPU-Gerät | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie einen Azure Arc-Datencontroller und Azure Data Services auf Ihrem Azure Stack Edge Pro-GPU-Gerät bereitstellen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/08/2021
ms.author: alkohli
ms.openlocfilehash: 9e56e37135c2ff73fb64d8afd5a852fd757f3e21
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989187"
---
# <a name="deploy-azure-data-services-on-your-azure-stack-edge-pro-gpu-device"></a>Bereitstellen von Azure Data Services auf Ihrem Azure Stack Edge Pro-GPU-Gerät


In diesem Artikel wird der Prozess des Erstellens eines Azure Arc-Datencontrollers und des anschließenden Bereitstellens von Azure Data Services auf Ihrem Azure Stack Edge Pro-GPU-Gerät beschrieben. 

Azure Arc-Datencontroller sind die lokale Steuerungsebene, die die Verwendung von Azure Data Services in von Kunden verwalteten Umgebungen ermöglicht. Nachdem Sie den Azure Arc-Datencontroller auf dem Kubernetes-Cluster erstellt haben, der auf Ihrem Azure Stack Edge Pro-Gerät ausgeführt wird, können Sie Azure Data Services wie SQL Managed Instance (Preview) auf diesem Datencontroller ausführen.

Der Prozess zum Erstellen eines Datencontrollers und anschließenden Bereitstellen einer SQL Managed Instance-Instanz umfasst die Verwendung von PowerShell und dem nativen Tool `kubectl`, das Befehlszeilenzugriff auf den Kubernetes-Cluster auf dem Gerät bietet.


## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie beginnen:

1. Sie verfügen über Zugriff auf ein Azure Stack Edge Pro-Gerät, und Sie haben das Gerät wie unter [Tutorial: Aktivieren von Azure Stack Edge Pro mit GPU](azure-stack-edge-gpu-deploy-activate.md) beschrieben aktiviert.

1. Sie haben die Computerolle auf dem Gerät aktiviert. Auf dem Gerät wurde auch ein Kubernetes-Cluster erstellt, als Sie gemäß den Anweisungen in [Konfigurieren der Computerolle auf einem Azure Stack Edge-Gerät](azure-stack-edge-gpu-deploy-configure-compute.md) Compute auf dem Gerät konfiguriert haben.

1. Sie besitzen den Kubernetes-API-Endpunkt von der Seite **Gerät** Ihrer lokalen Webbenutzeroberfläche. Weitere Informationen finden Sie in den Anweisungen unter [Abrufen des Kubernetes-API-Endpunkts](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints).

1. Sie haben Zugriff auf einen Client, der eine Verbindung mit Ihrem Gerät herstellt. 
    1. In diesem Artikel wird ein Windows-Clientsystem mit PowerShell 5.0 oder höher für den Zugriff auf das Gerät verwendet. Sie können einen beliebigen anderen Client mit einem [unterstützten Betriebssystem](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) verwenden. 
    1. Installieren Sie `kubectl` auf dem Client. Für die Clientversion:
        1. Identifizieren Sie die Kubernetes-Serverversion, die auf dem Gerät installiert ist. Navigieren Sie auf der lokalen Benutzeroberfläche des Geräts zur Seite **Softwareupdates**. Beachten Sie die **Kubernetes-Serverversion** auf dieser Seite.
        1. Laden Sie einen Client herunter, der nicht mehr als eine Nebenversion von der Masterversion abweicht. Die Clientversion kann aber gegenüber der Masterversion um bis zu einer Nebenversion höher sein. Beispiel: Die Masterversion v1.3 sollte mit den Knotenversionen v1.1, v1.2 und v1.3 sowie mit den Clientversionen v1.2, v1.3 und v1.4 funktionieren. Weitere Informationen zur Kubernetes-Clientversion finden Sie unter [Richtlinie zur Unterstützung der Kubernetes-Version und Versionsabweichung](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew).
    
1. Optional können Sie [Clienttools für das Bereitstellen und Verwalten von Azure Arc-fähigen Datendiensten installieren](../azure-arc/data/install-client-tools.md). Diese Tools sind nicht erforderlich, werden jedoch empfohlen.  
1. Stellen Sie sicher, dass Sie auf Ihrem Gerät über ausreichend Ressourcen für die Bereitstellung eines Datencontrollers und einer SQL Managed Instance-Instanz verfügen. Für den Datencontroller und eine SQL Managed Instance-Instanz benötigen Sie mindestens 16 GB RAM und 4 CPU-Kerne. Einen ausführlichen Leitfaden finden Sie unter [Mindestanforderungen für die Bereitstellung](../azure-arc/data/sizing-guidance.md#minimum-deployment-requirements).


## <a name="configure-kubernetes-external-service-ips"></a>Konfigurieren externer Dienst-IP-Adressen für Kubernetes

1. Navigieren Sie zur lokalen Webbenutzeroberfläche des Geräts und dann zu **Compute**.
1. Wählen Sie das für Compute aktivierte Netzwerk aus. 

    ![Seite „Compute“ auf der lokalen Benutzeroberfläche 2](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-1.png)

1. Stellen Sie sicher, dass Sie drei weitere externe Dienst-IP-Adressen für Kubernetes bereitstellen (zusätzlich zu den IP-Adressen, die Sie bereits für andere externe Dienste oder Container konfiguriert haben). Der Datencontroller verwendet zwei Dienst-IP-Adressen, und die dritte IP-Adresse wird beim Erstellen einer SQL Managed Instance-Instanz verwendet. Für jeden zusätzlichen Datendienst, den Sie bereitstellen, wird eine IP-Adresse benötigt. 

    ![Seite „Compute“ auf der lokalen Benutzeroberfläche 3](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-2.png)

1. Wenden Sie die Einstellungen an, und diese neuen IP-Adressen gelten sofort für einen bereits vorhandenen Kubernetes-Cluster. 


## <a name="deploy-azure-arc-data-controller"></a>Bereitstellen eines Azure Arc-Datencontrollers

Vor der Bereitstellung eines Datencontrollers müssen Sie einen Namespace erstellen.

### <a name="create-namespace"></a>Namespace erstellen 

Erstellen Sie einen neuen, dedizierten Namespace, in dem Sie den Datencontroller bereitstellen. Außerdem erstellen Sie einen Benutzer und erteilen diesem dann den Zugriff auf den von Ihnen erstellten Namespace. 

> [!NOTE]
> Sowohl für Namespace- als auch für Benutzernamen gelten die [Namenskonventionen für DNS-Unterdomänen](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names).

1. [Herstellen einer Verbindung mit der PowerShell-Schnittstelle](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Erstellen eines Namespaces Typ:

    `New-HcsKubernetesNamespace -Namespace <Name of namespace>`

1. Erstellen Sie einen Benutzer. Typ: 

    `New-HcsKubernetesUser -UserName <User name>`

1. Eine Konfigurationsdatei wird als Nur-Text angezeigt. Kopieren Sie diese Datei, und speichern Sie sie als *Konfigurationsdatei*. 

    > [!IMPORTANT]
    > Speichern Sie die Konfigurationsdatei nicht als *TXT*-Datei, sondern ohne Dateierweiterung.

1. Die Konfigurationsdatei sollte sich im Ordner `.kube` Ihres Benutzerprofils auf dem lokalen Computer befinden. Kopieren Sie die Datei in den Ordner in Ihrem Benutzerprofil.

    ![Speicherort der Konfigurationsdatei auf dem Client](media/azure-stack-edge-j-series-create-kubernetes-cluster/location-config-file.png)
1. Gewähren Sie dem Benutzer Zugriff auf den von Ihnen erstellten Namespace. Typ: 

    `Grant-HcsKubernetesNamespaceAccess -Namespace <Name of namespace> -UserName <User name>`

    Im Folgenden finden Sie eine Beispielausgabe der vorherigen Befehle. In diesem Beispiel erstellen Sie den Namespace `myadstest` und den Benutzer `myadsuser`, dem Sie dann Zugriff auf den Namespace gewähren.
    
    ```powershell
    [10.100.10.10]: PS>New-HcsKubernetesNamespace -Namespace myadstest
    [10.100.10.10]: PS>New-HcsKubernetesUser -UserName myadsuser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBD=======//snipped//=======VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.myasegpudev.wdshcsso.com:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: myadsuser
      name: myadsuser@kubernetes
    current-context: myadsuser@kubernetes
    kind: Config
    preferences: {}
    users:
    - name: myadsuser
      user:
        client-certificate-data: LS0tLS1CRUdJTiBDRV=========//snipped//=====EE9PQotLS0kFURSBLRVktLS0tLQo=
    
    [10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myadstest -UserName myadsuser
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS>
    ```
1. Fügen Sie der Datei „hosts“ auf Ihrem System einen DNS-Eintrag hinzu. 

    1. Führen Sie Notepad als Administrator aus, und öffnen Sie die Datei `hosts` unter `C:\windows\system32\drivers\etc\hosts`. 
    2. Verwenden Sie die Informationen, die Sie auf der Seite **Gerät** auf der lokalen Benutzeroberfläche (Voraussetzung) gespeichert haben, um den Eintrag in der Datei „hosts“ zu erstellen. 

        Kopieren Sie diesen Beispielendpunkt `https://compute.myasegpudev.microsoftdatabox.com/[10.100.10.10]`, um den folgenden Eintrag mit Geräte-IP-Adresse und DNS-Domäne zu erstellen: 

        `10.100.10.10 compute.myasegpudev.microsoftdatabox.com`

1. Starten Sie eine Cmd-Eingabeaufforderung oder eine PowerShell-Sitzung, um zu überprüfen, ob Sie eine Verbindung mit den Kubernetes-Pods herstellen können. Typ:
    
    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
    No resources found.
    PS C:\WINDOWS\system32>
    ```
Sie können nun Ihren Datencontroller und Datendienstanwendungen im Namespace bereitstellen und diese Anwendungen und deren Protokolle anschließend anzeigen.

### <a name="create-data-controller"></a>Erstellen eines Datencontrollers

Der Datencontroller ist eine Sammlung von Pods, die im Kubernetes-Cluster bereitgestellt werden, um eine API, den Controllerdienst, den Bootstrapper sowie die Überwachungsdatenbanken und -dashboards zur Verfügung zu stellen. Führen Sie die folgenden Schritte aus, um einen Datencontroller auf dem Kubernetes-Cluster zu erstellen, der auf dem Azure Stack Edge-Gerät im zuvor erstellten Namespace vorhanden ist.   

1. Sammeln Sie die folgenden Informationen, die Sie zum Erstellen eines Datencontrollers benötigen:

    
    |Column1  |Column2  |
    |---------|---------|
    |Name des Datencontrollers     |Dies ist ein beschreibender Name für den Datencontroller. Beispiel: `arctestdatacontroller`.         |
    |Benutzername des Datencontrollers     |Dies ist ein beliebiger Benutzername für den Datencontrolleradministrator. Der Benutzername und das Kennwort für den Datencontroller werden zur Authentifizierung bei der Datencontroller-API verwendet, um administrative Funktionen auszuführen.          |
    |Kennwort des Datencontrollers     |Dies ist ein Kennwort für den Datencontrolleradministrator. Wählen Sie ein sicheres Kennwort aus, und geben Sie es nur an Personen weiter, die Clusteradministratorberechtigungen benötigen.         |
    |Name des Kubernetes-Namespace     |Dies ist der Name des Kubernetes-Namespace, in dem Sie den Datencontroller erstellen möchten.         |
    |Azure-Abonnement-ID     |Dies ist die Azure-Abonnement-GUID für den Ort, an dem die Datencontrollerressource in Azure erstellt werden soll.         |
    |Azure-Ressourcengruppenname     |Dies ist der Name der Ressourcengruppe, in der die Datencontrollerressource in Azure erstellt werden soll.         |
    |Azure-Standort     |Dies ist der Azure-Standort, an dem die Metadaten der Datencontrollerressource in Azure gespeichert werden. Eine Liste der verfügbaren Regionen finden Sie unter Globale Azure-Infrastruktur/Produkte nach Region.|


1. Herstellen einer Verbindung mit der PowerShell-Schnittstelle. Geben Sie Folgendes ein, um den Datencontroller zu erstellen: 

    ```powershell
    Set-HcsKubernetesAzureArcDataController -SubscriptionId <Subscription ID> -ResourceGroupName <Resource group name> -Location <Location without spaces> -UserName <User you created> -Password <Password to authenticate to Data Controller> -DataControllerName <Data Controller Name> -Namespace <Namespace you created>    
    ```
    Im Folgenden finden Sie eine Beispielausgabe der vorherigen Befehle.

    ```powershell
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS> 
    ```
    
    Die Bereitstellung dauert ungefähr fünf Minuten.

    > [!NOTE]
    > Der Datencontroller, der auf dem Azure Stack Edge Pro-Gerät auf dem Kubernetes-Cluster erstellt wurde, funktioniert im aktuellen Release nur im Modus „Getrennt“.

### <a name="monitor-data-creation-status"></a>Überwachen des Status der Datencontrollererstellung

1. Öffnen Sie ein weiteres PowerShell-Fenster.
1. Verwenden Sie den folgenden `kubectl`-Befehl, um den Erstellungsstatus des Datencontrollers zu überwachen. 

    ```powershell
    kubectl get datacontroller/<Data controller name> --namespace <Name of your namespace>
    ```
    Wenn der Controller erstellt wurde, sollte der Status `Ready` lauten.
    Hier finden Sie eine Beispielausgabe des vorherigen Befehls:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get datacontroller/arctestcontroller --namespace myadstest
    NAME                STATE
    arctestcontroller   Ready
    PS C:\WINDOWS\system32>
    ```
1. Verwenden Sie den `kubectl get svc -n <namespace>`-Befehl, um die IP-Adressen zu identifizieren, die den externen Diensten zugewiesen sind, die auf dem Datencontroller ausgeführt werden. Hier ist eine Beispielausgabe:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get svc -n myadstest
    NAME                      TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                                       AGE
    controldb-svc             ClusterIP      172.28.157.130   <none>        1433/TCP,8311/TCP,8411/TCP                    3d21h
    controller-svc            ClusterIP      172.28.123.251   <none>        443/TCP,8311/TCP,8301/TCP,8411/TCP,8401/TCP   3d21h
    controller-svc-external   LoadBalancer   172.28.154.30    10.57.48.63   30080:31090/TCP                               3d21h
    logsdb-svc                ClusterIP      172.28.52.196    <none>        9200/TCP,8300/TCP,8400/TCP                    3d20h
    logsui-svc                ClusterIP      172.28.85.97     <none>        5601/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdb-svc             ClusterIP      172.28.255.103   <none>        8086/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdc-svc             ClusterIP      172.28.208.191   <none>        8300/TCP,8400/TCP                             3d20h
    metricsui-svc             ClusterIP      172.28.158.163   <none>        3000/TCP,8300/TCP,8400/TCP                    3d20h
    mgmtproxy-svc             ClusterIP      172.28.228.229   <none>        443/TCP,8300/TCP,8311/TCP,8400/TCP,8411/TCP   3d20h
    mgmtproxy-svc-external    LoadBalancer   172.28.166.214   10.57.48.64   30777:30621/TCP                               3d20h
    sqlex-svc                 ClusterIP      None             <none>        1433/TCP                                      3d20h
    PS C:\WINDOWS\system32>
    ```

## <a name="deploy-sql-managed-instance"></a>Bereitstellen einer SQL Managed Instance-Instanz

Nachdem Sie den Datencontroller erfolgreich erstellt haben, können Sie eine Vorlage verwenden, um eine SQL Managed Instance-Instanz auf dem Datencontroller bereitzustellen.

### <a name="deployment-template"></a>Bereitstellungsvorlage

Verwenden Sie die folgende Bereitstellungsvorlage, um eine SQL Managed Instance-Instanz auf dem Datencontroller auf dem Gerät bereitzustellen.

```yml
apiVersion: v1
data:
    password: UGFzc3dvcmQx
    username: bXlhZHN1c2Vy
kind: Secret
metadata:
    name: sqlex-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
    name: sqlex
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
    service:
    type: LoadBalancer
    storage:
    backups:
        className: ase-node-local
        size: 5Gi
    data:
        className: ase-node-local
        size: 5Gi
    datalogs:
        className: ase-node-local
        size: 5Gi
    logs:
        className: ase-node-local
        size: 1Gi
```


#### <a name="metadata-name"></a>Metadatenname
    
Der Metadatenname ist der Name der SQL Managed Instance-Instanz. Der zugeordnete Pod im vorangehenden `deployment.yaml` wird `sqlex-n` genannt (`n` entspricht der Anzahl von Pods, die der Anwendung zugeordnet sind). 
    
#### <a name="password-and-username-data"></a>Kennwort- und Benutzernamendaten

Der Benutzername und das Kennwort für den Datencontroller werden zur Authentifizierung bei der Datencontroller-API verwendet, um administrative Funktionen auszuführen. Das Kubernetes-Geheimnis für den Benutzernamen und das Kennwort des Datencontrollers in der Bereitstellungsvorlage besteht aus Base64-codierten Zeichenfolgen. 

Sie können ein Onlinetool verwenden, um den gewünschten Benutzernamen und das zugehörige Kennwort mit einer Base64-Codierung zu versehen, oder Sie können abhängig von Ihrer Plattform integrierte CLI-Tools verwenden. Wenn Sie ein Base64-Onlinecodierungstool verwenden, geben Sie die Benutzernamen- und Kennwortzeichenfolgen (die Sie beim Erstellen des Datencontrollers eingegeben haben) im Tool an, das dann die entsprechenden Base64-codierten Zeichenfolgen generiert.
    
#### <a name="service-type"></a>Dienstart

Der Diensttyp sollte auf `LoadBalancer` festgelegt sein.
    
#### <a name="storage-class-name"></a>Name der Speicherklasse

Sie können die Speicherklasse auf dem Azure Stack Edge-Gerät identifizieren, die bei der Bereitstellung für Daten, Sicherungen, Datenprotokolle und Protokolle verwendet wird. Verwenden Sie den `kubectl get storageclass`-Befehl, um die auf Ihrem Gerät bereitgestellte Speicherklasse abzurufen.

```powershell
PS C:\WINDOWS\system32> kubectl get storageclass
NAME             PROVISIONER      RECLAIMPOLICY  VOLUMEBINDINGMODE     ALLOWVOLUMEEXPANSION   AGE
ase-node-local   rancher.io/local-path   Delete  WaitForFirstConsumer  false                  5d23h
PS C:\WINDOWS\system32>
```
In der obigen Beispielausgabe sollte die Speicherklasse `ase-node-local` auf Ihrem Gerät in der Vorlage angegeben werden.
 
#### <a name="spec"></a>spec

Sie können Ihre Anforderungen in Bezug auf den Arbeitsspeicher und die CPU im Abschnitt „spec“ von `deployment.yaml` angeben, um eine SQL Managed Instance-Instanz auf Ihrem Azure Stack Edge-Gerät zu erstellen. Jede SQL Managed Instance-Instanz muss wie im folgenden Beispiel gezeigt mindestens 2 GB Arbeitsspeicher und einen CPU-Kern anfordern. 

```yml
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
```  

Ausführliche Informationen zur Dimensionierung für Datencontroller und eine SQL Managed Instance-Instanz finden Sie unter [Dimensionierungsdetails verwalteter SQL-Instanzen](../azure-arc/data/sizing-guidance.md#sql-managed-instance-sizing-details).

### <a name="run-deployment-template"></a>Ausführen der Bereitstellungsvorlage

Führen Sie `deployment.yaml` mithilfe des folgenden Befehls aus:

```powershell
kubectl create -n <Name of namespace that you created> -f <Path to the deployment yaml> 
```

Hier finden Sie eine Beispielausgabe des folgenden Befehls:

```powershell
PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
No resources found.
PS C:\WINDOWS\system32> 
PS C:\WINDOWS\system32> kubectl create -n myadstest -f C:\azure-arc-data-services\sqlex.yaml  secret/sqlex-login-secret created
sqlmanagedinstance.sql.arcdata.microsoft.com/sqlex created
PS C:\WINDOWS\system32> kubectl get pods --namespace myadstest
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-mv2cd   1/1     Running   0          83m
control-w9s9l        2/2     Running   0          78m
controldb-0          2/2     Running   0          78m
controlwd-4bmc5      1/1     Running   0          64m
logsdb-0             1/1     Running   0          64m
logsui-wpmw2         1/1     Running   0          64m
metricsdb-0          1/1     Running   0          64m
metricsdc-fb5r5      1/1     Running   0          64m
metricsui-56qzs      1/1     Running   0          64m
mgmtproxy-2ckl7      2/2     Running   0          64m
sqlex-0              3/3     Running   0          13m
PS C:\WINDOWS\system32>
```

Der `sqlex-0`-Pod in der Beispielausgabe gibt den Status der SQL Managed Instance-Instanz an.

## <a name="remove-data-controller"></a>Entfernen des Datencontrollers

Löschen Sie zum Entfernen des Datencontrollers den dedizierten Namespace, in dem Sie ihn bereitgestellt haben.


```powershell
kubectl delete ns <Name of your namespace>
```


## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen einer zustandslosen Anwendung auf Ihrem Azure Stack Edge Pro-Gerät](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).
