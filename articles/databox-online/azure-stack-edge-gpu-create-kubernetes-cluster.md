---
title: Erstellen und Verwalten eines Kubernetes-Clusters auf einem Azure Stack Edge Pro-GPU-Gerät | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie einen Kubernetes-Cluster auf einem Azure Stack Edge Pro-GPU-Gerät über die Windows PowerShell-Schnittstelle erstellen und verwalten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 6f6d2b126cd9a0acbbbdb8d17ce7345bbf17b556
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2020
ms.locfileid: "94635907"
---
# <a name="connect-to-and-manage-a-kubernetes-cluster-via-kubectl-on-your-azure-stack-edge-pro-gpu-device"></a>Verbinden mit einem Kubernetes-Cluster und Verwalten des Clusters über kubectl auf Ihrem Azure Stack Edge Pro-GPU-Gerät

Wenn Sie die Computerolle konfigurieren, wird auf Ihrem Azure Stack Edge Pro-Gerät ein Kubernetes-Cluster erstellt. Nachdem der Kubernetes-Cluster erstellt wurde, können Sie über ein natives Tool wie *kubectl* eine Verbindung mit einem Clientcomputer herstellen und den Cluster lokal verwalten.

In diesem Artikel wird beschrieben, wie Sie eine Verbindung mit einem Kubernetes-Cluster auf Ihrem Azure Stack Edge Pro-Gerät herstellen und diese dann mit *kubectl* verwalten. 


## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie beginnen:

1. Sie haben Zugriff auf ein Azure Stack Edge Pro-Gerät.

2. Sie haben das Azure Stack Edge Pro-Gerät aktiviert, wie unter [Aktivieren von Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md) beschrieben.

3. Sie haben die Computerolle auf dem Gerät aktiviert. Auf dem Gerät wurde auch ein Kubernetes-Cluster erstellt, als Sie gemäß den Anweisungen in [Konfigurieren der Computerolle auf einem Azure Stack Edge-Gerät](azure-stack-edge-gpu-deploy-configure-compute.md) Compute auf dem Gerät konfiguriert haben.

4. Sie haben Zugriff auf ein Windows-Clientsystem mit PowerShell 5.0 oder höher, um auf das Gerät zuzugreifen. Sie können auch einen anderen Client mit einem [unterstützten Betriebssystem](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) verwenden. 

5. Sie besitzen den Kubernetes-API-Endpunkt von der Seite **Gerät** Ihrer lokalen Webbenutzeroberfläche. Weitere Informationen finden Sie in den Anweisungen unter [Abrufen des Kubernetes-API-Endpunkts](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints).


## <a name="connect-to-powershell-interface"></a>Herstellen einer Verbindung mit der PowerShell-Schnittstelle

Nachdem der Kubernetes-Cluster erstellt wurde, können Sie auf diesen Cluster zugreifen, um Namespaces und Benutzer zu erstellen und den Namespaces entsprechende Benutzer zuzuweisen. Dazu müssen Sie eine Verbindung mit der PowerShell-Schnittstelle des Geräts herstellen. Führen Sie diese Schritte auf dem Windows-Client aus, auf dem PowerShell ausgeführt wird.

[!INCLUDE [Connect to admin runspace](../../includes/azure-stack-edge-gateway-connect-minishell.md)]


## <a name="configure-cluster-access-via-kubernetes-rbac"></a>Konfigurieren des Clusterzugriffs über Kubernetes RBAC

Nachdem der Kubernetes-Cluster erstellt wurde, können Sie mit *kubectl* über die Befehlszeile auf den Cluster zugreifen. 

Bei diesem Ansatz erstellen Sie einen Namespace und einen Benutzer. Anschließend ordnen Sie den Benutzer dem Namespace zu. Sie müssen auch die Datei *config* abrufen, die es Ihnen ermöglicht, einen Kubernetes-Client zu verwenden. Damit können Sie direkt und ohne Verbindung mit der PowerShell-Schnittstelle Ihres Azure Stack Edge Pro-Geräts mit dem von Ihnen erstellten Kubernetes-Cluster kommunizieren.

1. Erstellen eines Namespaces Typ:

    `New-HcsKubernetesNamespace -Namespace <string>` 

    > [!NOTE]
    > Sowohl für Namespace- als auch für Benutzernamen gelten die [Namenskonventionen für DNS-Unterdomänen](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names).

    Hier ist eine Beispielausgabe:

    `[10.100.10.10]: PS> New-HcsKubernetesNamespace -Namespace "myasetest1"`

2. Erstellen Sie einen Benutzer, und rufen Sie eine Konfigurationsdatei ab. Typ:

    `New-HcsKubernetesUser -UserName <string>`

    > [!NOTE]
    > Sie können *aseuser* nicht als Benutzername verwenden, da er für einen Standardbenutzer reserviert ist, der dem IoT-Namespace für Azure Stack Edge Pro zugeordnet ist.

    Hier folgt ein Beispiel für die Ausgabe der Konfigurationsdatei:
   
    ```powershell
    [10.100.10.10]: PS> New-HcsKubernetesUser -UserName "aseuser1"
        apiVersion: v1
        clusters:
        - cluster:
            certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01ERXlPVEUyTlRFeE4xb1hEVE13TURFeU5qRTJOVEV4TjFvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTXNpCkdZUHB0U1VWcDhKZEdVcHE1MVBURWhsZm8wWkk3YXFBdUlrOHZWdUFCZHJQK1FBSzFxcEN1di93NjIwbUtpZ0QKak1aT3Q4QkREREppWHF6UDZRZm5Oc0U2VXBHMnh0YnYrcTZHV2R5K0t6WkxMbXlwWGY3VjlzZEJnejVKVDNvYQpIdzFja2NTUklHSlV3UWxTbklNaHJUS3JUNDZFUUp3d282TmlNUzZMZDZieVk3WkUrTGg3OS9aNEhLanhTRmhMClc5ZG8veThZR3FXUDZmZTFmMmVmSkhUeGtwR05HZE1UVjNuOFlCZ0pSRzdrNjh0N2MrZ1NhbUlVWVJpTUNSNFAKYlFxcFpscWYvV2REZEJHOFh6aDJ0M1l4SkVIMm00T0Z1cSsvUitMYm95aHdKbmNMdVJ5OEpNZWlwTEQ3UlN0QwpZTDNNR0EzN2JieTRyYm4zVzg4Q0F3RUFBYU1qTUNFd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNbzFwWlBtQzV1cmRPZUJhSWQ4eEQzRkxCMG8KTlErbXBXMWpDd0ZtY3h6dUtlWmRsNXc2N0tuS2JTcDR0TXo1cXg3bUtSc0UxcnBoWkh2VHlKUXg1ZFk2ZE1Kdgp5d2FQZjBpT05TNlU2cC9INE12U1dJaEtJZ1FuTnE1dDh4TjJCNnZpQW41RmZoRkx6WEQrUlZGSm42cnovWkZnCmV6MHpxTkNKYmcvelFucFROcmQ2cnFFRHpoSVFZOVdYVWQycFh3ZXRqUXJpMkpZamh4NmtEcTVoRkZTM0FLUnIKOWlQTVQxaWNkR1NUMFVvM1hIZ1k2ck45WGp3MHFrY2I0Sy83UlVVWlRvS3dKamROR3lNTnpad000L2puR0p5SwpQTE9ycU5Ddlkvb0lkVEM5eVZVY3VRbXVlR0VqT20xUnN1RDFHYVE0RTZwakppVWJpMVdrajJ1bFhOWT0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
            server: https://10.128.47.90:6443
            name: kubernetes
        contexts:
        - context:
            cluster: kubernetes
            user: aseuser1
            name: aseuser1@kubernetes
        current-context: aseuser1@kubernetes
        kind: Config
        preferences: {}
        users:
        - name: aseuser1
            user:
            client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwVENDQWJtZ0F3SUJBZ0lJWlFXcjY2cGFWSm93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBeE1qa3hOalV4TVRkYUZ3MHlNVEF4TWpneU1qVTJNVGRhTUJNeApFVEFQQmdOVkJBTVRDR0Z6WlhWelpYSXhNSUlCSWpBTkJna3Foa2lHOXcwQkFRRUZBQU9DQVE4QU1JSUJDZ0tDCkFRRUF4R3RDaXJ2cVhGYm5wVmtaYlVPRWQ3cXg2UzNVZ092MlhHRHNKM2VYWXN0bUxQVjMrQnlBcWwyN211L1AKaWdmaWt6MG9QSW1iYmtvcVJkamlYckxFZnk0N3dHcEhzdUhUOHNLY2tHTnJleFE2bXhaZ29xaU1nL2FuMUpMdwpiOFEvVnlQaWdVdUt6eVBseEhUZmlmSVM5MzR1VnZVZUc0dzlMRjAyZ2s2Nitpc0ZtanhsVmhseWRMNlc2UmZTCjl0OGpNMEFkdEpJL0xNbE13RHJJRVdFKzM4WDVNelJhQkJYNnlzNDFWSkZxekcwdW14dHdxN2pGOXp1UTE4ekIKalRZaDl3OWVKcDJwS2Fvak5tNE9SSDh4SzVSaUhocjJ2anFJWXkxRDd2WDh0b0U1K05HNmxHZjh5L1NvQnNRbQpmOG9vL1k3SEZmQXVGdlN6WUc1RUlQTFM4UUlEQVFBQm95Y3dKVEFPQmdOVkhROEJBZjhFQkFNQ0JhQXdFd1lEClZSMGxCQXd3Q2dZSUt3WUJCUVVIQXdJd0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNR1BxY0YzS1BCbHZ0K24KN1NOcGE3anhWYkhZVGxyNTgwVWxzek93WEwwVnVPUUlCYmN2djIzZk9HNkhDZlQ0bWxBU0JRWVNZcmpLMjJTTwpTWld4cjNQUDlhVzNHajkxc0ttSnc1ZUF1WFhQbUJpK1RWQzBvY0ZLaEQvZ0o1aC93YnBaVndpVjVyRWE5Kzc2CnhNcFAzRld6dG5tT1hPaEl6UFNlR3B4YWpwQXd3ZXd4QU0yb0xGRFZFcy9XTFFMODJZM3NFcE93NVNaSVJJNXMKUHhMUTVnV1ZPM2x2SXcwZ3IrdkJlanZSOUZKaWVuTWFRdGdjSVgyRmpDaDBRMHVYRkdsTVNXWEljbjRLRTR0TApQSFFMalRSVUwyVnRXcW1YZ1RBM3RzN01DcGNRTFdPZFJUYkpSejZCbkc1aXVwcDdOSlFvYW9YcWpNVk5DVDZCCllYMEd0Skk9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
            client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFb2dJQkFBS0NBUUVBeEd0Q2lydnFYRmJucFZrWmJVT0VkN3F4NlMzVWdPdjJYR0RzSjNlWFlzdG1MUFYzCitCeUFxbDI3bXUvUGlnZmlrejBvUEltYmJrb3FSZGppWHJMRWZ5NDd3R3BIc3VIVDhzS2NrR05yZXhRNm14WmcKb3FpTWcvYW4xSkx3YjhRL1Z5UGlnVXVLenlQbHhIVGZpZklTOTM0dVZ2VWVHNHc5TEYwMmdrNjYraXNGbWp4bApWaGx5ZEw2VzZSZlM5dDhqTTBBZHRKSS9MTWxNd0RySUVXRSszOFg1TXpSYUJCWDZ5czQxVkpGcXpHMHVteHR3CnE3akY5enVRMTh6QmpUWWg5dzllSnAycEthb2pObTRPUkg4eEs1UmlIaHIydmpxSVl5MUQ3dlg4dG9FNStORzYKbEdmOHkvU29Cc1FtZjhvby9ZN0hGZkF1RnZTellHNUVJUExTOFFJREFRQUJBb0lCQUVVSUVXM2kxMTQycU5raQo5RjNEWWZZV1pscTJZYjRoc0FjTmhWSGxwUTN5d0dsQ3FEUktDQ3BZSVF3MkJqSFR6WnpEM0xWU0E0K0NmMUxuCkE4QVdnaHJVcStsWE1QVzhpcG9DTGJaTlNzUUord0x3bld2dFl0MHFQaGZtd0p2M1UrK1RUQkwyOHNVVUw3ZVkKLzh0aWlhbno3ZU5mNklIMENyZmgxcnQ3WWhsemtRd1hBVHNScVJja0dMaTgrdGN5WnVzdGFhbENUSzBGRTdCaQpBUGE5a2w1SG56eCs4TTcvNWladHkwTUIxYWpWMnlGblBkUmlKSFVCb1AxVVV0QUthYjVZU0RvNllkZ2pIUTRHCjNWN1l1YWZobnVFMXA0VVIvUkloVVdjRlVVaTFBOFpZMFdnd1BDTmhnMWpQZU5vb2Y1UHpRbEY1OTRBREVwUUYKOFR2bG92RUNnWUVBOWZZbUxyY0tlQ0JiMTFoQVhoTi91Z1RTbU5xNnpFL1pPSWl6M0xwckdjRDhvWDdCVW9GcgplelkxbktSS2tkczE5OERnVjlQZUhuNzllQTRoMjM5RkIwNFFhMUJBdUVMRzRsdHJ3VlNxaFBENUR6YkcrSEhSCnJtYThVMEpUSmVVS0tJVjRUUGxlTzFtK2tjbkRJVXY1ckpwZDVXU3RvcUhXdk9RZkEvRUF0VlVDZ1lFQXpHOTcKTitCZVVvbFNiREttVUNGdTdPZGhYSXJYR3RnSEorZ2JOMDlnSHRURG5PY0IxZ1NzNkpZa1FPQU9qbWFxK05lRAp5SUF1NytheWlFRmpyT2tzTGhkSTREUXNkWFZveFFGVko1V1JwWlk3UTVRaFZpYUR2enR4NDlzSDlKSkplM2U0Cnl3NWdpNGkxKy90MnY2eWRKcWdNQ0xxOHlEdFRrcE9PSitkbkp5MENnWUJwZ3lpcURaZU9KTU9CUTdpSkl2QSsKQ21lVmJ1K0hTaEd6TU9HSHBPamc2V3IybEh1Mk94S3lqblM5TjdWTmtLNDhGQitwVFpnUm1RUi9CZ0Q4T2tLUQplYXFOZnFYazViQ1AxZ3dKcVpwazRVTFdoZmNoQ1NLY0lESlZ2VFFTSTRrU0RQK29kYWs0Nkt6WnVhWGRtTXdJCmdVZ2FhZkFhdmpaeVhhSDRmT0NDNlFLQmdHVXJCaDh3dVh5KzJEc1RGWnF4OE9McjNoS2Q0clUyRXRSODJIc1cKbk1xbEgraVZxU0x3VFdFTWJBUnUzTVU3cVlCYnBxdWlRNWdVNG1UcmR4Z3FpK0tEUTEwd2RJL3IrbDBEdTlCTApCRGlkajlaeGg4M0tZWWhSTXBzLzJULys1TDVsRU4zcnozczl2RkZtcisxS3pycENqeklDdDBtZmtrd0hHV0pGCjhaWkJBb0dBVXB3aUIrcWlHbkpxU1FtZHNSZFVabGFBaTRpbGhaa01RYTRHem95ZFQ3OTVHTm44ZThBRjd3WHMKTGpyYjdEV1FwakdCMnZpUlkySUZBVmIyKzZsdDlwOVJRMTZnSmxpNU5ZRXVvQWRoWXBsVWdBZGFHWHNGNHdabwo3SHFHTHBGdmUxVU5Gb0dQdkxpWUNrUFVYdGduQ3dNb0R2SEpKNzVYMXl6ckh6cmxUS1k9Ci0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==  
        
    [10.100.10.10]: PS>
    ```
    

3. Eine Konfigurationsdatei wird als Nur-Text angezeigt. Kopieren Sie diese Datei, und speichern Sie sie als *Konfigurationsdatei*. 

    > [!IMPORTANT]
    > Speichern Sie die Konfigurationsdatei nicht als *TXT*-Datei, sondern ohne Dateierweiterung.

4. Die Konfigurationsdatei sollte sich im Ordner `.kube` Ihres Benutzerprofils auf dem lokalen Computer befinden. Kopieren Sie die Datei in den Ordner in Ihrem Benutzerprofil.

    ![Speicherort der Konfigurationsdatei auf dem Client](media/azure-stack-edge-j-series-create-kubernetes-cluster/location-config-file.png)

5. Ordnen Sie den Namespace dem Benutzer zu, den Sie erstellt haben. Typ:

    `Grant-HcsKubernetesNamespaceAccess -Namespace <string> -UserName <string>`

    Hier ist eine Beispielausgabe:

    `[10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace "myasetest1" -UserName "aseuser1"`

    Wenn Sie über die Konfigurationsdatei verfügen, benötigen Sie keinen physischen Zugriff auf den Cluster. Wenn Ihr Client die IP-Adresse des Azure Stack Edge Pro-Geräts pingen kann, sollten Sie den Cluster mithilfe von *kubectl*-Befehlen weiterleiten können.

6. Starten Sie eine neue PowerShell-Sitzung auf dem Client. Sie müssen nicht mit der Geräteschnittstelle verbunden sein. Mit dem folgenden Befehl können Sie jetzt `kubectl` auf dem Client installieren:

    ```powershell
    PS C:\windows\system32> curl https://storage.googleapis.com/kubernetes-release/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    
    PS C:\windows\system32>
    ```
    Wenn z. B. auf dem Kubernetes-Masterknoten v1.15.2 ausgeführt wurde, installieren Sie v1.15.2 auf dem Client.

    > [!IMPORTANT]
    > Laden Sie einen Client herunter, der nicht mehr als eine Nebenversion von der Masterversion abweicht. Die Clientversion kann aber gegenüber der Masterversion um bis zu einer Nebenversion höher sein. Beispiel: Die Masterversion v1.3 sollte mit den Knotenversionen v1.1, v1.2 und v1.3 sowie mit den Clientversionen v1.2, v1.3 und v1.4 funktionieren. Weitere Informationen zur Kubernetes-Clientversion finden Sie unter [Richtlinie zur Unterstützung der Kubernetes-Version und Versionsabweichung](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew). Weitere Informationen zur Kubernetes-Serverversion unter Azure Stack Edge Pro finden Sie unter „Abrufen der Kubernetes-Serverversion“.<!-- insert link-->
    > Manchmal wird `kubectl` auf Ihrem System vorinstalliert, wenn Sie Docker für Windows oder andere Tools ausführen. Es ist wichtig, die bestimmte Version von `kubectl` (wie in diesem Abschnitt angegeben) herunterzuladen, um mit diesem Kubernetes-Cluster arbeiten zu können. 

    Die Installation dauert einige Minuten.

7. Überprüfen Sie, ob die installierte Version der heruntergeladenen Version entspricht. Sie sollten den absoluten Pfad zu dem Speicherort angeben, an dem die `kubectl.exe` auf Ihrem System installiert wurde.
    
    ```powershell
    PS C:\Users\myuser> C:\windows\system32\kubectl.exe version
    Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
    Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
    PS C:\Users\myuser>
    ``` 
    
    Weitere Informationen zu `kubectl`-Befehlen, die zum Verwalten des Kubernetes-Clusters verwendet werden, finden Sie unter [Übersicht über kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

8. Fügen Sie der Datei „hosts“ auf Ihrem System einen DNS-Eintrag hinzu. 

    1. Führen Sie Notepad als Administrator aus, und öffnen Sie die Datei `hosts` unter `C:\windows\system32\drivers\etc\hosts`. 
    2. Verwenden Sie die Informationen, die Sie im früheren Schritt auf der Seite **Gerät** in der lokalen Benutzeroberfläche gespeichert haben, um den Eintrag in der Datei „hosts“ zu erstellen. 

        Kopieren Sie diesen Beispielendpunkt `https://compute.asedevice.microsoftdatabox.com/[10.100.10.10]`, um den folgenden Eintrag mit Geräte-IP-Adresse und DNS-Domäne zu erstellen: 

        `10.100.10.10 compute.asedevice.microsoftdatabox.com`

9. Geben Sie Folgendes ein, um zu überprüfen, ob Sie eine Verbindung mit den Kubernetes-Pods herstellen können:
    
    ```powershell
    PS C:\Users\myuser> kubectl get pods -n "myasetest1"
    No resources found.
    PS C:\Users\myuser>
    ```
Sie können Ihre Anwendungen jetzt im Namespace bereitstellen und dann diese Anwendungen und ihre Protokolle anzeigen.

> [!IMPORTANT]   
> Es gibt viele Befehle, die Sie nicht ausführen können, z. B. die Befehle, für die Sie Administratorzugriff benötigen. Sie können nur Vorgänge ausführen, die im Namespace zulässig sind.


## <a name="remove-kubernetes-cluster"></a>Entfernen eines Kubernetes-Clusters

Zum Entfernen des Kubernetes-Clusters müssen Sie die Computekonfiguration entfernen.

Ausführliche Anweisungen finden Sie unter [Entfernen der Computekonfiguration](azure-stack-edge-j-series-manage-compute.md#remove-compute-configuration).
   

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen einer zustandslosen Anwendung auf Ihrem Azure Stack Edge Pro-Gerät](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).
