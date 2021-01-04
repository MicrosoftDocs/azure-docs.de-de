---
title: Aktivieren einer Edge-Containerregistrierung auf einem Azure Stack Edge Pro-GPU-Gerät
description: Erfahren Sie, wie Sie eine lokale Edge-Containerregistrierung auf einem Azure Stack Edge Pro-GPU-Gerät aktivieren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: bccb6fa33007082737997c7282fb286c38e3bbd7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465408"
---
# <a name="enable-edge-container-registry-on-your-azure-stack-edge-pro-gpu-device"></a>Aktivieren einer Edge-Containerregistrierung auf Ihrem Azure Stack Edge Pro-GPU-Gerät

In diesem Artikel wird beschrieben, wie Sie die Edge-Containerregistrierung aktivieren und innerhalb des Kubernetes-Clusters auf Ihrem Azure Stack Edge Pro-Gerät verwenden. In dem in diesem Artikel verwendeten Beispiel wird erläutert, wie Sie ein Image aus einer Quellregistrierung (in diesem Fall der Microsoft Container Registry) in die Registrierung auf dem Azure Stack Edge-Gerät (die Edge-Containerregistrierung) pushen.

### <a name="about-edge-container-registry"></a>Informationen zur Edge-Containerregistrierung

Containerisierte Computeanwendungen werden auf Containerimages ausgeführt, und diese Images werden in Registrierungen gespeichert. Registrierungen können öffentlich, z. B. Docker Hub, oder privat sein oder durch einen Cloudanbieter verwaltet werden, z. B. Azure Container Registry. Weitere Informationen finden Sie unter [Informationen zu Registrierungen, Repositorys und Images](../container-registry/container-registry-concepts.md).

Eine Edge-Containerregistrierung stellt auf Ihrem Azure Stack Edge Pro-Gerät ein Repository am Edge bereit. Sie können diese Registrierung zum Speichern und Verwalten Ihrer privaten Containerimages verwenden.

In einer Umgebung mit mehreren Knoten können Containerimages heruntergeladen und einmal in die Edge-Containerregistrierung gepusht werden. Alle Edgeanwendungen können die Edge-Containerregistrierung für nachfolgende Bereitstellungen verwenden.


## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie beginnen:

1. Sie haben Zugriff auf ein Azure Stack Edge Pro-Gerät.

1. Sie haben das Azure Stack Edge Pro-Gerät aktiviert, wie unter [Aktivieren von Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md) beschrieben.

1. Sie haben die Computerolle auf dem Gerät aktiviert. Auf dem Gerät wurde auch ein Kubernetes-Cluster erstellt, als Sie gemäß den Anweisungen in [Konfigurieren der Computerolle auf einem Azure Stack Edge-Gerät](azure-stack-edge-gpu-deploy-configure-compute.md) Compute auf dem Gerät konfiguriert haben.

1. Sie besitzen den Kubernetes-API-Endpunkt von der Seite **Gerät** Ihrer lokalen Webbenutzeroberfläche. Weitere Informationen finden Sie in den Anweisungen unter [Abrufen des Kubernetes-API-Endpunkts](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints).

1. Sie haben Zugriff auf ein Clientsystem mit einem [unterstützten Betriebssystem](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device). Wenn Sie einen Windows-Client verwenden, sollte für den Zugriff auf das Gerät auf dem System mindestens PowerShell 5.0 ausgeführt werden.

    1. Wenn Sie Ihre eigenen Containerimages per Pull und Push übertragen möchten, müssen Sie sicherstellen, dass der Docker-Client auf dem System installiert ist. Wenn Sie einen Windows-Client verwenden, [installieren Sie Docker Desktop unter Windows](https://docs.docker.com/docker-for-windows/install/).  


## <a name="enable-container-registry-as-add-on"></a>Aktivieren der Containerregistrierung als Add-On

Der erste Schritt besteht darin, die Edge-Containerregistrierung als Add-On zu aktivieren.

1. [Stellen Sie eine Verbindung mit der PowerShell-Schnittstelle des Geräts her.](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) 

1. Geben Sie Folgendes ein, um die Containerregistrierung als Add-On zu aktivieren: 

    `Set-HcsKubernetesContainerRegistry`
    
    Dieser Vorgang kann mehrere Minuten dauern.

    Nachfolgend finden Sie die Beispielausgabe dieses Befehls:  
            
    ```powershell
    [10.128.44.40]: PS>Set-HcsKubernetesContainerRegistry
    Operation completed successfully. Use Get-HcsKubernetesContainerRegistryInfo for credentials    
    ```
            
1. Geben Sie Folgendes ein, um Details zur Containerregistrierung abzurufen:

    `Get-HcsKubernetesContainerRegistryInfo`

    Nachfolgend finden Sie die Beispielausgabe dieses Befehls:  
    
    ```powershell
    [10.128.44.40]: PS> Get-HcsKubernetesContainerRegistryInfo
                
    Endpoint                                   IPAddress    Username     Password
    --------                                   ---------    --------     --------
    ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 10.128.44.41 ase-ecr-user i3eTsU4zGYyIgxV
    ``` 

1. Notieren Sie sich den Benutzernamen und das Kennwort aus der Ausgabe von `Get-HcsKubernetesContainerRegistryInfo`. Diese Anmeldeinformationen werden verwendet, um sich beim Pushen von Images bei der Edge-Containerregistrierung anzumelden.         


## <a name="manage-container-registry-images"></a>Verwalten der Images der Containerregistrierung

Möglicherweise möchten Sie außerhalb Ihres Azure Stack Edge-Geräts auf die Containerregistrierung zugreifen. Vielleicht möchten Sie auch Images in die Registrierung pushen oder daraus pullen.

Führen Sie die folgenden Schritte aus, um auf die Edge-Containerregistrierung zuzugreifen:

1. Besorgen Sie sich die Endpunktdetails für die Edge-Containerregistrierung.
    1. Wechseln Sie auf der lokalen Benutzeroberfläche des Geräts zu **Gerät**.
    1. Suchen Sie den **Edge container registry endpoint** (Endpunkt der Edge-Containerregistrierung).
        ![Endpunkt der Edge-Containerregistrierung auf der Seite „Gerät“](media/azure-stack-edge-gpu-edge-container-registry/get-edge-container-registry-endpoint-1.png) 
    1. Kopieren Sie diesen Endpunkt, und erstellen Sie einen entsprechenden DNS-Eintrag in der Datei `C:\Windows\System32\Drivers\etc\hosts` des Clients, um eine Verbindung mit dem Endpunkt der Edge-Containerregistrierung herzustellen. 

        <IP address of the Kubernetes main node>    <Edge container registry endpoint> 
        
        ![DNS-Eintrag für den Endpunkt der Edge-Containerregistrierung hinzufügen](media/azure-stack-edge-gpu-edge-container-registry/add-domain-name-service-entry-hosts-1.png)    

1. Laden Sie das Edge-Containerregistrierungszertifikat von der lokalen Benutzeroberfläche herunter. 
    1. Wechseln Sie auf der lokalen Benutzeroberfläche des Geräts zu **Zertifikate**.
    1. Suchen Sie den Eintrag für **Edge container registry certificate** (Edge-Containerregistrierungszertifikat). Wählen Sie rechts neben diesem Eintrag den Link **Herunterladen** aus, um das Zertifikat der Edge-Containerregistrierung auf Ihr Clientsystem herunterzuladen, das Sie für den Zugriff auf Ihr Gerät verwenden möchten. 

        ![Edge-Containerregistrierungs-Endpunktzertifikat herunterladen](media/azure-stack-edge-gpu-edge-container-registry/download-edge-container-registry-endpoint-certificate-1.png)  

1. Installieren Sie das heruntergeladene Zertifikat auf dem Client. Wenn Sie einen Windows-Client verwenden, führen Sie die folgenden Schritte aus: 
    1. Wählen Sie das Zertifikat und dann im **Zertifikatimport-Assistenten** als Speicherort **Lokaler Computer** aus. 

        ![Zertifikat installieren 1](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-1.png) 
    
    1. Installieren Sie das Zertifikat auf dem lokalen Computer im vertrauenswürdigen Stammspeicher. 

        ![Zertifikat installieren 2](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-2.png) 

1. Nachdem das Zertifikat installiert wurde, starten Sie den Docker-Client auf Ihrem System neu.

1. Melden Sie sich bei der Edge-Containerregistrierung an. Typ:

    `docker login <Edge container registry endpoint> -u <username> -p <password>`

    Geben Sie den Endpunkt der Edge-Containerregistrierung von der Seite **Geräte** und den Benutzernamen und das Kennwort aus der Ausgabe von `Get-HcsKubernetesContainerRegistryInfo` an. 

1. Verwenden Sie die Push- oder Pull-Befehle von Docker, um Containerimages in die Containerregistrierung zu pushen oder daraus zu pullen.
 
    1. Pullen Sie ein Image aus dem Image der Microsoft Container Registry. Typ:
        
        `docker pull <Full path to the container image in the Microsoft Container Registry>`
       
    1. Erstellen Sie einen Alias des gepullten Images mit dem vollqualifiziertem Pfad zu Ihrer Registrierung.

        `docker tag <Path to the image in the Microsoft container registry> <Path to the image in the Edge container registry/Image name with tag>`

    1. Übertragen Sie das Image per Push in Ihre Registrierung.
    
        `docker push <Path to the image in the Edge container registry/Image name with tag>`

    1. Führen Sie das Image aus, das Sie in Ihre Registrierung gepusht haben.
    
        `docker run -it --rm -p 8080:80 <Path to the image in the Edge container registry/Image name with tag>`

        Nachfolgend finden Sie eine Beispielausgabe des Pull- und Push-Befehls:

        ```powershell
        PS C:\WINDOWS\system32> docker login ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 -u ase-ecr-user -p 3bbo2sOtDe8FouD
        WARNING! Using --password via the CLI is insecure. Use --password-stdin.
        Login Succeeded
        PS C:\WINDOWS\system32> docker pull mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        1.17.5-alpine: Pulling from oss/nginx/nginx
        Digest: sha256:5466bbc0a989bd1cd283c0ba86d9c2fc133491ccfaea63160089f47b32ae973b
        Status: Image is up to date for mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        PS C:\WINDOWS\system32> docker tag mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        PS C:\WINDOWS\system32> docker push ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        The push refers to repository [ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx]
        bba7d2385bc1: Pushed
        77cae8ab23bf: Pushed
        2.0: digest: sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff size: 739
        PS C:\WINDOWS\system32> docker run -it --rm -p 8080:80 ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        2020/11/10 00:00:49 [error] 6#6: *1 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 172.17.0.1, server: localhost, request: "GET /favicon.ico HTTP/1.1", host: "localhost:8080", referrer: "http://localhost:8080/"
        172.17.0.1 - - [10/Nov/2020:00:00:49 +0000] "GET /favicon.ico HTTP/1.1" 404 555 "http://localhost:8080/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.183 Safari/537.36" "-"
        ^C
        PS C:\WINDOWS\system32>    
        ```
    
1. Navigieren Sie zu `http://localhost:8080`, um den ausgeführten Container anzuzeigen. In diesem Fall sehen Sie, dass der nginx-Webserver ausgeführt wird.

    ![Den ausgeführten Container anzeigen](media/azure-stack-edge-gpu-edge-container-registry/view-running-container-1.png)

    Drücken Sie zum Beenden und Entfernen des Containers die Tastenkombination `Control+C`.

 

## <a name="use-edge-container-registry-images-via-kubernetes-pods"></a>Verwenden von Edge-Containerregistrierungsimages über Kubernetes-Pods

Sie können jetzt das Image, das Sie in Ihre Edge-Containerregistrierung gepusht haben, innerhalb der Kubernetes-Pods bereitstellen.

1. Um das Image bereitzustellen, müssen Sie den Clusterzugriff über *kubectl* konfigurieren. Erstellen Sie einen Namespace und einen Benutzer, gewähren Sie dem Benutzer Zugriff auf den Namespace, und rufen Sie eine *config*-Datei ab. Stellen Sie sicher, dass Sie eine Verbindung mit den Kubernetes-Pods herstellen können. 
    
    Befolgen Sie alle Schritte unter [Verbinden mit einem Kubernetes-Cluster und Verwalten des Clusters über kubectl auf Ihrem Azure Stack Edge Pro-GPU-Gerät](azure-stack-edge-gpu-create-kubernetes-cluster.md). 

    Im Folgenden eine Beispielausgabe für einen Namespace auf Ihrem Gerät, von dem aus der Benutzer auf den Kubernetes-Cluster zugreifen kann.

    ```powershell
    [10.128.44.40]: PS>New-HcsKubernetesNamespace -Namespace myecr
    [10.128.44.40]: PS>New-HcsKubernetesUser -UserName ecruser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01URXdOVEF6TkRJek1Gb1hEVE13TVRFd016QXpOREl6TUZvd0ZURVRNQkVnNjOVRLWndCQ042cm1XQms2eXFwcXI1MUx6bApTaXMyTy91UEJ2YXNSSUUzdzgrbmEwdG1aTERZZ2F6MkQwMm42Q29mUmtyUTR2d1lLTnR1MlpzR3pUdz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.dbe-hw6h1t2.microsoftdatabox.com:6443
        name: kubernetes
        ===================CUT=========================================CUT==============
        client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJYmVWRGJSTzZ3ell3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURFeE1EVXdNelF5TXpCYUZ3MHlNVEV4TURreU16UTRNal
        ===================CUT=========================================CUT==============
        DMVUvN3lFOG5UU3k3b2VPWitUeHdzCjF1UDByMjhDZ1lCdHdRY0ZpcFh1blN5ak16dTNIYjhveFI2V3VWWmZldFFKNElKWEFXOStVWGhKTFhyQ2x4bUcKWHRtbCt4UU5UTzFjQVNKRVZWVDd6Tjg2ay9kSU43S3JIVkdUdUxlUDd4eGVjV2VRcWJrZEVScUsxN0liTXpiVApmbnNxc0dobEdmLzdmM21kTGtyOENrcWs5TU5aM3MvUVIwRlFCdk94ZVpuUlpTeDVGbUR5S1E9PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=

    [10.128.44.40]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myecr -UserName ecruser
    [10.128.44.40]: PS>kubectl get pods -n "myecr"
    No resources found.
    PS C:\WINDOWS\system32>
    ```  

2. Die Geheimnisse zum Pullen von Images sind bereits in allen Kubernetes-Namespaces auf Ihrem Gerät festgelegt. Geheimnisse können mit dem Befehl `get secrets` abgerufen werden. Hier ist eine Beispielausgabe:

    ```powershell
    PS C:\WINDOWS\system32> .\kubectl.exe get secrets -n myecr
    NAME                  TYPE                                  DATA   AGE
    ase-ecr-credentials   kubernetes.io/dockerconfigjson        1      99m
    default-token-c7kww   kubernetes.io/service-account-token   3      107m
    sec-smbcredentials    microsoft.com/smb                     2      99m
    PS C:\WINDOWS\system32>   
    ```    

3. Stellen Sie mithilfe von kubectl einen Pod für Ihren Namespace bereit. Verwenden Sie `yaml` wie folgt. 

    Ersetzen Sie das Image `<image-name>` durch das Image, das in die Containerregistrierung gepusht wurde. Verweisen Sie auf die Geheimnisse in Ihren Namespaces unter Verwendung von imagePullSecrets mit dem Namen `ase-ecr-credentials`.
    
    ```yml
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
    spec:
      containers:
      - name: nginx
        image: ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        imagePullPolicy: Always
      imagePullSecrets:
      - name: ase-ecr-credentials
    ```

4. Wenden Sie die Bereitstellung im erstellten Namespace mit dem Befehl „apply“ an. Stellen Sie sicher, dass der Container ausgeführt wird. Hier ist eine Beispielausgabe:
   
    ```yml
    PS C:\Windows\System32> .\kubectl.exe apply -f .\deployment.yml -n myecr
    pod/nginx configured
    PS C:\Windows\System32> .\kubectl.exe get pods -n myecr
    NAME    READY   STATUS    RESTARTS   AGE
    nginx   1/1     Running   0          27m
    PS C:\Windows\System32>
    ```

## <a name="delete-container-registry-images"></a>Löschen der Images der Containerregistrierung

Speicher für die Edge-Containerregistrierung wird auf einer lokalen Freigabe in Ihrem Azure Stack Edge Pro-Gerät gehostet, das durch den verfügbaren Speicher auf dem Gerät beschränkt ist. Sie sind dafür zuständig, nicht verwendete Docker-Images über die HTTP v2-API von Docker (https://docs.docker.com/registry/spec/api/) aus der Containerregistrierung zu löschen.  

Führen Sie die folgenden Schritte aus, um mindestens ein Containerimage zu entfernen:

1. Legen Sie den Imagenamen auf das Image fest, das Sie löschen möchten.

    ```powershell
    PS C:\WINDOWS\system32> $imageName="nginx"    
    ```

1. Legen Sie den Benutzernamen und das Kennwort der Containerregistrierung als PS-Anmeldeinformationen fest.

    ```powershell
    PS C:\WINDOWS\system32> $username="ase-ecr-user"
    PS C:\WINDOWS\system32> $password="3bbo2sOtDe8FouD"
    PS C:\WINDOWS\system32> $securePassword = ConvertTo-SecureString $password -AsPlainText -Force
    PS C:\WINDOWS\system32> $credential = New-Object System.Management.Automation.PSCredential ($username, $securePassword)    
    ```

1. Listen Sie die dem Image zugeordneten Tags auf.

    ```powershell
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/nginx/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32>    
    ```
  
1. Listen Sie den Digest auf, der dem Tag zugeordnet ist, das Sie löschen möchten. Dabei werden $tags aus der Ausgabe des obigen Befehls verwendet. Wenn Sie über mehrere Tags verfügen, wählen Sie eines aus, und verwenden Sie es im nächsten Befehl.

    ```powershell
    PS C:\WINDOWS\system32> $response = Invoke-WebRequest -Method Head -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$tags" -Headers @{ 'Accept' = 'application/vnd.docker.distribution.manifest.v2+json' }
    PS C:\WINDOWS\system32> $digest = $response.Headers['Docker-Content-Digest']
    PS C:\WINDOWS\system32> $digest
    sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff
    PS C:\WINDOWS\system32>    
    ```
1. Löschen Sie das Image mit dem Digest von „image:tag“.

    ```powershell
    PS C:\WINDOWS\system32> Invoke-WebRequest -Method Delete -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$digest" | Select-Object -ExpandProperty StatusDescription    
    ```

Nachdem Sie die nicht verwendeten Images gelöscht haben, wird der Speicherplatz, der den nicht referenzierten Images zugeordnet ist, automatisch durch einen nachts ausgeführten Prozess freigegeben. 

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen einer zustandslosen Anwendung auf Ihrem Azure Stack Edge Pro-Gerät](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).
