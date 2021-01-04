---
title: Bereitstellen von VMs auf Ihrem Azure Stack Edge Pro-GPU-Gerät mit der Azure CLI und Python
description: Hier wird beschrieben, wie virtuelle Computer (Virtual Machines, VMs) auf einem Azure Stack Edge Pro-GPU-Gerät mit der Azure CLI und Python erstellt und verwaltet werden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/07/2020
ms.author: alkohli
ms.openlocfilehash: c12935a4b0b9cbbf088ba7b9c5ea804be0ce85a1
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96447364"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-using-azure-cli-and-python"></a>Bereitstellen von VMs auf Ihrem Azure Stack Edge Pro-GPU-Gerät mit der Azure CLI und Python

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-overview](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-overview.md)]

In diesem Tutorial wird beschrieben, wie Sie einen virtuellen Computer auf Ihrem Azure Stack Edge Pro-Gerät mit der Azure-Befehlszeilenschnittstelle (Command Line Interface, CLI) und Python erstellen und verwalten.

## <a name="vm-deployment-workflow"></a>VM-Bereitstellungsworkflow

Der Bereitstellungsworkflow ist im folgenden Diagramm dargestellt.

![VM-Bereitstellungsworkflow](media/azure-stack-edge-j-series-deploy-virtual-machine-powershell/vm-workflow_r.svg)

Die allgemeine Zusammenfassung des Bereitstellungsworkflows lautet wie folgt:

1. Herstellen einer Verbindung mit Azure Resource Manager
2. Erstellen einer Ressourcengruppe
3. Speicherkonto erstellen
4. Hinzufügen eines Blob-URI zur Datei „hosts“
5. Installieren von Zertifikaten
6. Hochladen einer VHD-Datei
7. Erstellen verwalteter Datenträger aus der VHD
8. Erstellen eines VM-Images aus dem verwalteten Imagedatenträger
9. Erstellen eines virtuellen Computers mit zuvor erstellten Ressourcen
10. Erstellen eines VNET
11. Erstellen einer VNIC mithilfe der VNet-Subnetz-ID

Eine ausführliche Erläuterung des Workflowdiagramms finden Sie unter [Bereitstellen von VMs auf Ihrem Azure Stack Edge Pro-Gerät mithilfe von Azure PowerShell](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md). Informationen zum Herstellen einer Verbindung mit Azure Resource Manager finden Sie unter [Herstellen einer Verbindung mit Azure Resource Manager mithilfe von Azure PowerShell](azure-stack-edge-j-series-connect-resource-manager.md).

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit dem Erstellen und Verwalten eines virtuellen Computers auf Ihrem Azure Stack Edge Pro-Gerät mit der Azure CLI und Python beginnen, müssen Sie sicherstellen, dass Sie die in den folgenden Schritten aufgeführten Voraussetzungen erfüllt haben:

1. Sie haben die Netzwerkeinstellungen auf Ihrem Azure Stack Edge Pro-Gerät wie unter [Schritt 1: Konfigurieren des Azure Stack Edge Pro-Geräts](azure-stack-edge-j-series-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device) beschrieben festgelegt.

2. Sie haben eine Netzwerkschnittstelle für Compute erstellt. Diese Netzwerkschnittstellen-IP wird verwendet, um einen virtuellen Switch für die VM-Bereitstellung zu erstellen. Die folgenden Schritte führen Sie durch den Prozess:

    1. Navigieren Sie zu **Compute**. Wählen Sie die Netzwerkschnittstelle aus, die Sie verwenden möchten, um einen virtuellen Switch zu erstellen.

        > [!IMPORTANT] 
        > Sie können nur einen Port für Compute konfigurieren.

    2. Aktivieren Sie Compute auf der Netzwerkschnittstelle. Azure Stack Edge Pro erstellt und verwaltet einen virtuellen Switch, der dieser Netzwerkschnittstelle entspricht.

    <!--If you decide to use another network interface for compute, make sure that you:

    - Delete all the VMs that you have deployed using Azure Resource Manager.

    - Delete all virtual network interfaces and the virtual network associated with this network interface.

    - You can now enable another network interface for compute.-->

3. Sie haben alle Zertifikate auf Ihrem Azure Stack Edge Pro-Gerät und im vertrauenswürdigen Speicher des Clients erstellt und installiert. Führen Sie das unter [Schritt 2: Erstellen und Installieren von Zertifikaten](azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates) beschriebene Verfahren aus.

4. Sie haben ein Base-64-codiertes *CER*-Zertifikat (PEM-Format) für Ihr Azure Stack Edge Pro-Gerät erstellt. Dieses wurde bereits als Signaturkette auf das Gerät hochgeladen und im vertrauenswürdigen Stammspeicher auf dem Client installiert. Dieses Zertifikat ist auch im *PEM*-Format erforderlich, damit Python auf diesem Client funktioniert.

    Konvertieren Sie dieses Zertifikat mit dem Befehl `certutil` in das PEM-Format. Sie müssen diesen Befehl in dem Verzeichnis ausführen, das Ihr Zertifikat enthält.

    ```powershell
    certutil.exe <SourceCertificateName.cer> <DestinationCertificateName.pem>
    ```
    Im Folgenden ist die Verwendung des Beispielbefehls dargestellt:

    ```powershell
    PS C:\Certificates> certutil.exe -encode aze-root.cer aze-root.pem
    Input Length = 2150
    Output Length = 3014
    CertUtil: -encode command completed successfully.
    PS C:\Certificates>
    ```    
    Sie fügen dieses PEM-Zertifikat später auch zum Python-Speicher hinzu.

5. Sie haben die IP-Adresse des Geräts auf der Seite **Netzwerk** in der lokalen Webbenutzeroberfläche des Geräts zugewiesen. Sie müssen diese IP-Adresse zu Folgendem hinzufügen:

    - Der Hostdatei auf dem Client ODER
    - Der DNS-Serverkonfiguration
    
    > [!IMPORTANT]
    > Es wird empfohlen, dass Sie die DNS-Serverkonfiguration für die Endpunktnamensauflösung ändern.

    1. Starten Sie **Editor** als Administrator (Administratorrechte sind erforderlich, um die Datei zu speichern), und öffnen Sie anschließend die Datei **hosts** unter `C:\Windows\System32\Drivers\etc`.
    
        ![Datei „hosts“ in Windows-Explorer](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file.png)
    
    2. Fügen Sie die folgenden Einträge zur Datei **hosts** hinzu, und fügen Sie die entsprechenden Werte für Ihr Gerät ein:
    
        ```
        <Device IP> login.<appliance name>.<DNS domain>
        <Device IP> management.<appliance name>.<DNS domain>
        <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
        ```
    3. Verwenden Sie die folgende Abbildung als Referenz. Speichern Sie die Datei **hosts**.

        ![Datei „hosts“ in Editor](media/azure-stack-edge-j-series-deploy-virtual-machine-cli-python/hosts-screenshot-boxed.png)

6. [Laden Sie das Python-Skript](https://aka.ms/ase-vm-python) herunter, das in diesem Verfahren verwendet wird.

## <a name="step-1-set-up-azure-clipython-on-the-client"></a>Schritt 1: Einrichten der Azure CLI/Python auf dem Client

### <a name="verify-profile-and-install-azure-cli"></a>Überprüfen des Profils und Installieren der Azure CLI

<!--1. Verify the API profile of the client and identify which version of the modules and libraries to include on your client. In this example, the client system will be running Azure Stack 1904 or later. For more information, see [Azure Resource Manager API profiles](/azure-stack/user/azure-stack-version-profiles?view=azs-1908#azure-resource-manager-api-profiles).-->

1. Installieren Sie die Azure CLI auf Ihrem Client. In diesem Beispiel wurde die Azure CLI 2.0.80 installiert. Führen Sie den Befehl `az --version` aus, um die Version der Azure CLI zu überprüfen.

    Nachfolgend sehen Sie eine Beispielausgabe des o. g. Befehls:

    ```powershell
    PS C:\windows\system32> az --version
    azure-cli                         2.0.80
    
    command-modules-nspkg              2.0.3
    core                              2.0.80
    nspkg                              3.0.4
    telemetry                          1.0.4
    Extensions:
    azure-cli-iot-ext                  0.7.1
    
    Python location 'C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe'
    Extensions directory 'C:\.azure\cliextensions'
    
    Python (Windows) 3.6.6 (v3.6.6:4cf1f54eb7, Jun 27 2018, 02:47:15) [MSC v.1900 32 bit (Intel)]
    
    Legal docs and information: aka.ms/AzureCliLegal
    
    Your CLI is up-to-date.
    
    Please let us know how we are doing: https://aka.ms/clihats
    PS C:\windows\system32>
    ```

    Wenn Sie nicht über die Azure CLI verfügen, laden Sie die Azure CLI herunter, und [installieren Sie sie unter Windows](/cli/azure/install-azure-cli-windows?view=azure-cli-latest). Sie können die Azure CLI mithilfe der Windows-Eingabeaufforderung oder über Windows PowerShell ausführen.

2. Notieren Sie sich den Python-Speicherort der CLI. Er ist erforderlich, damit Sie den Speicherort des vertrauenswürdigen Stammzertifikatspeichers für die Azure CLI bestimmen können.

3. Zum Ausführen des Beispielskripts, das in diesem Artikel verwendet wird, benötigen Sie die folgenden Versionen der Python-Bibliothek:

    ```powershell
    azure-common==1.1.23
    azure-mgmt-resource==2.1.0
    azure-mgmt-network==2.7.0
    azure-mgmt-compute==5.0.0
    azure-mgmt-storage==1.5.0
    azure-storage-blob==1.2.0rc1
    haikunator
    msrestazure==0.6.2
    ```
    Führen Sie zum Installieren der Versionen den folgenden Befehl aus:

    ```powershell
    .\python.exe -m pip install haikunator
    ```

    Die folgende Beispielausgabe zeigt die Installation von Haikunator:

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe -m pip install haikunator

    Collecting haikunator
      Downloading https://files.pythonhosted.org/packages/43/fa/130968f1a1bb1461c287b9ff35c630460801783243acda2cbf3a4c5964a5/haikunator-2.1.0-py2.py3-none-any.whl
    
    Installing collected packages: haikunator
    Successfully installed haikunator-2.1.0
    You are using pip version 10.0.1, however version 20.0.1 is available.
    You should consider upgrading using the 'python -m pip install --upgrade pip' command.
    
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> 
    ```

    Die folgende Beispielausgabe zeigt die Installation von PIP für `msrestazure`: 
    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe -m pip install msrestazure==0.6.2
    Requirement already satisfied: msrestazure==0.6.2 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (0.6.2)
    Requirement already satisfied: msrest<2.0.0,>=0.6.0 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from msrestazure==0.6.2) (0.6.10)
    === CUT ===========================  CUT ==================================
    Requirement already satisfied: cffi!=1.11.3,>=1.8 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from cryptography>=1.1.0->adal<2.0.0,>=0.6.0->msrestazure==0.6.2) (1.13.2)
    Requirement already satisfied: pycparser in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from cffi!=1.11.3,>=1.8->cryptography>=1.1.0->adal<2.0.0,>=0.6.0->msrestazure==0.6.2) (2.18)
    You are using pip version 10.0.1, however version 20.0.1 is available.
    You should consider upgrading using the 'python -m pip install --upgrade pip' command.
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

### <a name="trust-the-azure-stack-edge-pro-ca-root-certificate"></a>Einstufen des Zertifizierungsstellen-Stammzertifikats für Azure Stack Edge Pro als vertrauenswürdig

1. Suchen Sie den Speicherort des Zertifikats auf Ihrem Computer. Der Speicherort kann in Abhängigkeit davon variieren, wo Sie `az cli` installiert haben. Führen Sie Windows PowerShell als Administrator aus. Wechseln Sie zu dem Pfad, wo `az cli` Python installiert hat: `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe`.

    Geben Sie folgenden Befehl ein, um den Speicherort des Zertifikats zu erhalten:

    ```powershell
    .\python -c "import certifi; print(certifi.where())"
    ```
    
    Das Cmdlet gibt den Speicherort des Zertifikats zurück, wie unten gezeigt:  
        
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python -c "import certifi; print(certifi.where())"
    C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```
      
    Notieren Sie sich diesen Speicherort, da Sie ihn später verwenden werden: `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem`

2. Stufen Sie das Zertifizierungsstellen-Stammzertifikat für Azure Stack Edge Pro als vertrauenswürdig ein, indem Sie es an das vorhandene Python-Zertifikat anfügen. Geben Sie den Pfad an, wo Sie das PEM-Zertifikat zuvor gespeichert haben.

    ```powershell
    $pemFile = "<Path to the pem format certificate>"
    ```
    Ein Beispielpfad wäre etwa „C:\VM-scripts\rootteam3device.pem“.
    
    Geben Sie anschließend die folgende Reihe von Befehlen in Windows PowerShell ein:

    ```powershell
    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
        $root.Import($pemFile)
        
    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry= [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")
    
    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText
    
    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry
    
    Write-Host "Python Cert store was updated to allow the Azure Stack Edge Pro CA root certificate"
    ```
    
### <a name="connect-to-azure-stack-edge-pro"></a>Herstellen einer Verbindung mit Azure Stack Edge Pro

1. Registrieren Sie Ihre Azure Stack Edge Pro-Umgebung, indem Sie den Befehl `az cloud register` ausführen.

    In einigen Szenarien wird die direkte ausgehende Internetkonnektivität durch einen Proxy oder eine Firewall geleitet, die das Abfangen von SSL erzwingen. In diesen Fällen kann beim Befehl „az cloud register“ ein Fehler wie etwa \"Endpunkte können nicht aus der Cloud abgerufen werden\" auftreten. Als Problemumgehung dieses Fehlers legen Sie die folgenden Umgebungsvariablen in Windows PowerShell fest:

    ```powershell
    $ENV:AZURE_CLI_DISABLE_CONNECTION_VERIFICATION = 1 
    $ENV:ADAL_PYTHON_SSL_NO_VERIFY = 1
    ```

2. Legen Sie Umgebungsvariablen für das Skript für den Azure Resource Manager-Endpunkt, den Speicherort, an dem die Ressourcen erstellt werden, und den Pfad zum Speicherort der Quell-VHD fest. Der Speicherort für die Ressourcen wird auf allen Azure Stack Edge Pro-Geräten korrigiert und auf `dbelocal` festgelegt. Sie müssen auch die Adresspräfixe und die private IP-Adresse angeben. Alle folgenden Umgebungsvariablen sind Werte, die auf Ihren Werten basieren, mit Ausnahme von `AZURE_RESOURCE_LOCATION`, was mit `"dbelocal"`hartcodiert wird.

    ```powershell
    $ENV:ARM_ENDPOINT = "https://management.team3device.teatraining1.com"
    $ENV:AZURE_RESOURCE_LOCATION = "dbelocal"
    $ENV:VHD_FILE_PATH = "C:\Downloads\Ubuntu1604\Ubuntu13.vhd"
    $ENV:ADDRESS_PREFIXES = "5.5.0.0/16"
    $ENV:PRIVATE_IP_ADDRESS = "5.5.174.126"
    ```

3. Registrieren Sie Ihre Umgebung. Verwenden Sie beim Ausführen von „az cloud register“ die folgenden Parameter:

    | Wert | BESCHREIBUNG | Beispiel |
    | --- | --- | --- |
    | Umgebungsname | Der Name der Umgebung, mit der Sie eine Verbindung herstellen möchten | Geben Sie einen Namen an, wie z. B. `aze-environ`. |
    | Resource Manager-Endpunkt | Die URL lautet `https://Management.<appliancename><dnsdomain>`. <br> Damit Sie diese URL abrufen können, navigieren Sie zur Seite **Geräte** in der lokalen Webbenutzeroberfläche Ihres Geräts. |Beispiel: `https://management.team3device.teatraining1.com`.  |
    
    ```powershell
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.<appliance name>.<DNS domain>"
    ```
    Im Folgenden sehen Sie ein Beispiel für die Verwendung des o. g. Befehls:
    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud register -n az-new-env --endpoint-resource-manager "https://management.team3device.teatraining1.com"
    ```
    
    
4. Legen Sie die aktive Umgebung mithilfe der folgenden Befehle fest:

    ```powershell
    az cloud set -n <EnvironmentName>
    ```
    Im Folgenden sehen Sie ein Beispiel für die Verwendung des o. g. Befehls:

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud set -n az-new-env
    Switched active cloud to 'az-new-env'.
    Use 'az login' to log in to this cloud.
    Use 'az account set' to set the active subscription.
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

4. Melden Sie sich bei Ihrer Azure Stack Edge Pro-Umgebung an, indem Sie den Befehl `az login` ausführen. Sie können sich entweder als Benutzer oder als [Dienstprinzipal](../active-directory/develop/app-objects-and-service-principals.md) bei der Azure Stack Edge Pro-Umgebung anmelden.

   Führen Sie die folgenden Schritte aus, um sich als *Benutzer* anzumelden:

   Sie können entweder den Benutzernamen und das Kennwort direkt im Befehl `az login` eingeben oder die Authentifizierung über einen Browser ausführen. Sie müssen das letztgenannte Verfahren wählen, wenn für Ihr Konto die mehrstufige Authentifizierung aktiviert ist.

   Im Folgenden sehen Sie ein Beispiel für die Verwendung von `az login`:
    
    ```powershell
    PS C:\Certificates> az login -u EdgeARMuser
    ```
   Nach der Verwendung des Anmeldebefehls werden Sie zur Eingabe eines Kennworts aufgefordert. Geben Sie das Azure Resource Manager-Kennwort ein.

   Das folgende Beispiel zeigt die Ausgabe einer erfolgreichen Anmeldung nach Angabe des Kennworts:  
   
   ```powershell
   PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az login -u EdgeARMuser
   Password:
   [
        {
            "cloudName": "az-new-env",
            "id": "A4257FDE-B946-4E01-ADE7-674760B8D1A3",
            "isDefault": true,
            "name": "Default Provider Subscription",
            "state": "Enabled",
            "tenantId": "c0257de7-538f-415c-993a-1b87a031879d",
            "user": {
                "name": "EdgeArmUser@localhost",
                "type": "user"
            }
        }
   ]
   PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
   ```
   Notieren Sie sich die Werte `id` und `tenantId`, da diese Ihrer Azure Resource Manager-Abonnement-ID bzw. Ihrer Azure Resource Manager-Mandanten-ID entsprechen und in einem späteren Schritt verwendet werden.
       
   Die folgenden Umgebungsvariablen müssen so festgelegt werden, dass sie als *Dienstprinzipal* funktionieren:

   ```
   $ENV:ARM_TENANT_ID = "c0257de7-538f-415c-993a-1b87a031879d"
   $ENV:ARM_CLIENT_ID = "cbd868c5-7207-431f-8d16-1cb144b50971"
   $ENV:ARM_CLIENT_SECRET - "<Your Azure Resource Manager password>"
   $ENV:ARM_SUBSCRIPTION_ID = "A4257FDE-B946-4E01-ADE7-674760B8D1A3"
   ```

   Ihre Azure Resource Manager-Client-ID ist hartcodiert. Ihre Azure Resource Manager-Mandanten-ID und Azure Resource Manager-Abonnement-ID sind beide in der Ausgabe Befehls `az login` vorhanden, den Sie zuvor ausgeführt haben. Der geheime Azure Resource-Clientschlüssel ist das Azure Resource Manager-Kennwort, das Sie festgelegt haben.

   Weitere Informationen finden Sie unter [Azure Resource Manager-Kennwort](azure-stack-edge-j-series-set-azure-resource-manager-password.md).

5. Ändern Sie das Profil in Version „2019-03-01-hybrid“. Führen Sie den folgenden Befehl aus, um die Profilversion zu ändern:

    ```powershell
    az cloud update --profile 2019-03-01-hybrid
    ```

    Im Folgenden sehen Sie ein Beispiel für die Verwendung von `az cloud update`:

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud update --profile 2019-03-01-hybrid
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

## <a name="step-2-create-a-vm"></a>Schritt 2: Erstellen einer VM

Ein Python-Skript wird für Sie bereitgestellt, um einen virtuellen Computer zu erstellen. In Abhängigkeit davon, ob Sie als Benutzer angemeldet oder als Dienstprinzipal festgelegt sind, übernimmt das Skript die Eingabe entsprechend und erstellt einen virtuellen Computer.

1. Führen Sie das Python-Skript aus dem Verzeichnis aus, in dem Python installiert ist.

    `.\python.exe example_dbe_arguments_name_https.py cli`

2. Wenn das Skript ausgeführt wird, dauert das Hochladen der VHD 20–30 Minuten. Um den Fortschritt des Uploadvorgangs anzuzeigen, können Sie Azure Storage-Explorer oder AzCopy verwenden.

    Im Folgenden finden Sie eine Beispielausgabe einer erfolgreichen Ausführung des Skripts. Das Skript erstellt alle Ressourcen in einer Ressourcengruppe, verwendet diese Ressourcen, um einen virtuellen Computer zu erstellen, und löscht schließlich die Ressourcengruppe einschließlich aller erstellten Ressourcen.

    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe example_dbe_arguments_name_https.py cli
    
    Create Resource Group
    Create a storage account
    Uploading to Azure Stack Storage as blob:
            ubuntu13.vhd
    
    Listing blobs...
            ubuntu13.vhd
    
    VM image resource id:
                /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/azure-sample-group-virtual-machines118/providers/Microsoft.Compute/images/UbuntuImage
    
    Create Vnet
    Create Subnet
    Create NIC
    Creating Linux Virtual Machine
    Tag Virtual Machine
    Create (empty) managed Data Disk
    Get Virtual Machine by Name
    Attach Data Disk
    Detach Data Disk
    Deallocating the VM (to prepare for a disk resize)
    Update OS disk size
    Start VM
    Restart VM
    Stop VM
    
    List VMs in subscription
            VM: VmName118
    
    List VMs in resource group
            VM: VmName118
    
    Delete VM
    All example operations completed successfully!
    
    Delete Resource Group
    Deleted: azure-sample-group-virtual-machines118
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ``` 


## <a name="next-steps"></a>Nächste Schritte

[Häufig verwendete Azure CLI-Befehle zum Verwalten von Azure-Ressourcen](../virtual-machines/linux/cli-manage.md)