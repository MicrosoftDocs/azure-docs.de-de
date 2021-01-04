---
title: Konfigurieren von VPN auf Ihrem Azure Stack Edge Mini R-Gerät mithilfe von Azure PowerShell
description: Erfahren Sie, wie Sie VPN auf Ihrem Azure Stack Edge Mini R-Gerät mithilfe eines Azure PowerShell-Skripts zum Erstellen von Azure-Ressourcen konfigurieren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 11/17/2020
ms.author: alkohli
ms.openlocfilehash: 763ccd397d8cd704ca161032e65f17979bccb53b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465757"
---
# <a name="configure-vpn-on-your-azure-stack-edge-mini-r-device-via-azure-powershell"></a>Konfigurieren von VPN auf Ihrem Azure Stack Edge Mini R-Gerät mithilfe von Azure PowerShell

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

Die Option „VPN“ bietet für Daten während der Übertragung über *TLS* von Ihrem Azure Stack Edge Mini R- oder Azure Stack Edge Pro R-Gerät in Azure eine zweite Verschlüsselungsebene. Sie können VPN auf Ihrem Azure Stack Edge Mini R-Gerät über das Azure-Portal oder Azure PowerShell konfigurieren. 

In diesem Artikel werden die zum Konfigurieren eines P2S-VPN (Point-to-Site) auf Ihrem Azure Stack Edge Mini R-Gerät erforderlichen Schritte beschrieben, wobei die Konfiguration in der Cloud mithilfe eines Azure PowerShell-Skripts erstellt wird. Die Konfiguration auf dem Azure Stack Edge-Gerät erfolgt auf der lokalen Benutzeroberfläche.

## <a name="about-vpn-setup"></a>Informationen zur VPN-Einrichtung

Mithilfe einer P2S-VPN-Gatewayverbindung können Sie von einem einzelnen Clientcomputer oder Ihrem Azure Stack Edge Mini R-Gerät aus eine sichere Verbindung mit Ihrem virtuellen Netzwerk herstellen. Sie starten die P2S-Verbindung auf dem Clientcomputer oder Gerät. Die P2S-Verbindung verwendet in diesem Fall IKEv2-VPN, eine auf Standards basierende IPSec-VPN-Lösung.

Der Workflow umfasst üblicherweise folgende Schritte:

1. Erfüllen der Voraussetzungen
2. Einrichten der erforderlichen Ressourcen in Azure
    1. Erstellen und Konfigurieren eines virtuellen Netzwerks und benötigter Subnetze 
    2. Erstellen und Konfigurieren eines Azure-VPN-Gateways (Gateway für virtuelle Netzwerke)
    3. Einrichten von Azure Firewall und Hinzufügen von Netzwerk- und App-Regeln
    4. Erstellen von Azure-Routingtabellen und Hinzufügen von Routen
    5. Aktivieren eines Point-to-Site-VPN-Gateways
        1. Hinzufügen des Clientadresspools
        2. Konfigurieren des Tunneltyps
        3. Konfigurieren des Authentifizierungstyps
        4. Erstellen eines Zertifikats
        5. Hochladen des Zertifikats
    6. Herunterladen des Telefonbuchs
3. Einrichten des VPN auf der lokalen Webbenutzeroberfläche des Geräts 
    1. Bereitstellen des Telefonbuchs
    2. Bereitstellen von Azure-Diensttags (JSON)


Details zu diesen Schritten finden Sie in den folgenden Abschnitten.

## <a name="configure-prerequisites"></a>Erfüllen der Voraussetzungen

- Sie benötigen Zugriff auf ein Azure Stack Edge Mini R-Gerät, das gemäß den Anweisungen unter [Installieren eines Azure Stack Edge Mini R-Geräts](azure-stack-edge-mini-r-deploy-install.md) installiert wurde. Auf diesem Gerät wird eine P2S-Verbindung mit Azure hergestellt. 

- Sie benötigen Zugriff auf ein gültiges Azure-Abonnement, das in Azure für den Dienst Azure Stack Edge aktiviert ist. Erstellen Sie im Rahmen dieses Abonnements eine entsprechende Ressource in Azure, um Ihr Azure Stack Edge Mini R-Gerät zu verwalten.  

- Sie haben Zugriff auf einen Windows-Client, über den Sie auf das Azure Stack Edge Mini R-Gerät zugreifen. Mit diesem Client können Sie die Konfiguration in der Cloud programmgesteuert erstellen.

    1. Um die erforderliche PowerShell-Version auf Ihrem Windows-Client zu installieren, führen Sie die folgenden Befehle aus:

        ```azurepowershell
        Install-Module -Name Az -AllowClobber -Scope CurrentUser 
        Import-Module Az.Accounts
        ```
    2. Führen Sie den folgenden Befehl aus, um eine Verbindung mit Ihrem Azure-Konto und -Abonnement herzustellen:

        ```azurepowershell
        Connect-AzAccount 
        Set-AzContext -Subscription "<Your subscription name>"
        ```
        Geben Sie den Namen des Azure-Abonnements an, das Sie mit Ihrem Azure Stack Edge Mini R-Gerät zum Konfigurieren von VPN verwenden.

    3. [Laden Sie das Skript herunter](https://aka.ms/ase-vpn-deployment), das zum Erstellen der Konfiguration in der Cloud erforderlich ist. Das Skript führt Folgendes aus:
        
        - Erstellen eines virtuelles Azure-Netzwerks und der folgenden Subnetze: *GatewaySubnet* und *AzureFirewallSubnet*
        - Erstellen und Konfigurieren eines Azure-VPN-Gateways
        - Erstellen und Konfigurieren eines lokalen Azure-Gateways
        - Erstellen und Konfigurieren einer Azure-VPN-Verbindung zwischen dem Azure-VPN-Gateway und dem lokalen Netzwerkgateway
        - Einrichten von Azure Firewall und Hinzufügen von Netzwerk- und App-Regeln
        - Erstellen einer Azure-Routingtabelle und Hinzufügen von Routen

    4. Erstellen Sie im Azure-Portal die Ressourcengruppe, in der die Azure-Ressourcen erstellt werden sollen. Wechseln Sie im Azure-Portal zur Liste der Dienste. Wählen Sie **Ressourcengruppe** und dann **+ Hinzufügen** aus. Geben Sie die Abonnementinformationen und den Namen der Ressourcengruppe an, und wählen Sie dann **Erstellen** aus. Wenn Sie zu dieser Ressourcengruppe wechseln, darf sie zu diesem Zeitpunkt keine Ressourcen enthalten.

        ![Azure-Ressourcengruppe](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-resource-group-1.png)
    
    5. Sie benötigen für das Azure Stack Edge Mini R-Gerät ein Base64-codiertes Zertifikat im Format `.cer`. Dieses Zertifikat muss im Format `pfx` mit einem privaten Schlüssel auf das Azure Stack Edge-Gerät hochgeladen werden. Dieses Zertifikat muss auch im vertrauenswürdigen Stammverzeichnis des Speichers auf dem Client installiert sein, der versucht, die P2S-Verbindung herzustellen.

## <a name="use-the-script"></a>Verwenden des Skripts

Zuerst ändern Sie die Datei `parameters-p2s.json`, indem Sie Ihre Parameter eingeben. Als Nächstes führen Sie das Skript unter Verwendung der geänderten JSON-Datei aus.

Jeder dieser Schritte wird in den folgenden Abschnitten ausführlich erläutert.

### <a name="download-service-tags-file"></a>Herunterladen der Datei mit den Diensttags

Möglicherweise verfügen Sie im Ordner, in den Sie das Skript heruntergeladen haben, bereits über die Datei `ServiceTags.json`. Andernfalls können Sie die Datei mit den Diensttags herunterladen.

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>Ändern der Datei „parameters“

Der erste Schritt besteht darin, die Datei `parameters-p2s.json` zu ändern und die Änderungen zu speichern. 

Geben Sie für die Azure-Ressourcen, die Sie erstellen, die folgenden Namen an:

|Parametername  |BESCHREIBUNG  |
|---------|---------|
|virtualNetworks_vnet_name    | Name der Azure Virtual Network-Instanz        |
|azureFirewalls_firewall_name     | Name der Azure Firewall-Instanz        |
|routeTables_routetable_name     | Name der Azure-Routingtabelle        |
|publicIPAddresses_VNGW_public_ip_name     | Name der öffentlichen IP-Adresse Ihres Gateways für virtuelle Netzwerke       |
|virtualNetworkGateways_VNGW_name    | Name des Azure-VPN-Gateways (Name des virtuellen Netzwerks)        |
|publicIPAddresses_firewall_public_ip_name     | Name der öffentlichen IP-Adresse Ihrer Azure Firewall-Instanz         |
|location     |Dies ist die Region, in der Sie Ihr virtuelles Netzwerk erstellen möchten. Wählen Sie die Region aus, die Ihrem Gerät zugeordnet ist.         |
|RouteTables_routetable_onprem_name| Dies ist der Name der zusätzlichen Routingtabelle, mit der die Firewall Pakete an das Azure Stack Edge-Gerät zurückleiten kann. Das Skript erstellt zwei weitere Routen und ordnet *Default* und *FirewallSubnet* dieser Routingtabelle zu.|

Geben Sie die folgenden IP-Adressen und Adressräume für die erstellten Azure-Ressourcen an, einschließlich des virtuellen Netzwerks und der zugehörigen Subnetze (*Default*, *Firewall*, *GatewaySubnet*).

|Parametername  |BESCHREIBUNG  |
|---------|---------|
|VnetIPv4AddressSpace    | Dies ist der Ihrem virtuellen Netzwerk zugeordnete Adressraum. Geben Sie den IP-Adressbereich des VNET als privaten IP-Adressbereich (https://en.wikipedia.org/wiki/Private_network#Private_IPv4_addresses) ) ein.     |
|DefaultSubnetIPv4AddressSpace    |Dies ist der dem Subnetz `Default` in Ihrem virtuellen Netzwerk zugeordnete Adressraum.         |
|FirewallSubnetIPv4AddressSpace    |Dies ist der dem Subnetz `Firewall` in Ihrem virtuellen Netzwerk zugeordnete Adressraum.          |
|GatewaySubnetIPv4AddressSpace    |Dies ist der `GatewaySubnet` in Ihrem virtuellen Netzwerk zugeordnete Adressraum.          |
|GatewaySubnetIPv4bgpPeeringAddress    | Dies ist die IP-Adresse, die für die BGP-Kommunikation reserviert ist und auf dem Adressraum basiert, der `GatewaySubnet` für Ihr virtuelles Netzwerk zugeordnet ist.          |
|ClientAddressPool    | Diese IP-Adresse wird für den Adresspool in der P2S-Konfiguration im Azure-Portal verwendet.         |
|PublicCertData     | Öffentliche Zertifikatdaten werden von der VPN Gateway-Instanz zum Authentifizieren von P2S-Clients verwendet, die eine Verbindung damit herstellen. Installieren Sie das Stammzertifikat, um die Zertifikatdaten zu erhalten. Stellen Sie sicher, dass das Zertifikat Base64-codiert ist und die Erweiterung. „.cer“ hat. Öffnen Sie dieses Zertifikat, und kopieren Sie den Text im Zertifikat zwischen ==BEGIN CERTIFICATE== und ==END CERTIFICATE== in eine fortlaufende Zeile.     |



### <a name="run-the-script"></a>Führen Sie das Skript aus.

Führen Sie die folgenden Schritte aus, um die geänderte Datei `parameters-p2s.json` zu verwenden und das Skript zum Erstellen von Azure-Ressourcen auszuführen.

1. Führen Sie PowerShell aus. Wechseln Sie zum Verzeichnis mit dem Skript.

3. Führen Sie das Skript aus.

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    > [!NOTE]
    > In dieser Version funktioniert das Skript nur in der Region „USA, Osten“.

    Zum Ausführen des Skripts müssen die folgenden Informationen eingegeben werden:

    
    |Parameter  |BESCHREIBUNG  |
    |---------|---------|
    |Standort     |Dies ist die Region, in der die Azure-Ressourcen erstellt werden müssen.         |
    |AzureAppRuleFilePath     | Dies ist der Dateipfad für `appRule.json`.       |
    |AzureIPRangesFilePath     |Dies ist die JSON-Datei mit den Diensttags, die Sie im vorherigen Schritt heruntergeladen haben.         |
    |ResourceGroupName     | Dies ist der Name der Ressourcengruppe, in der alle Azure-Ressourcen erstellt werden.        |
    |AzureDeploymentName    |Dies ist der Name Ihrer Azure-Bereitstellung.         |
    |NetworkRuleCollectionName            | Dies ist der Name für die Sammlung aller Netzwerkregeln, die erstellt und Ihrer Azure Firewall-Instanz hinzugefügt werden.             |
    |Priorität            | Dies ist die Priorität, die allen erstellten Netzwerk- und Anwendungsregeln zugewiesen wird.              |
    |AppRuleCollectionName            |Dies ist der Name der Sammlung aller Anwendungsregeln, die erstellt und der Azure Firewall-Instanz hinzugefügt werden.                |


    Nachfolgend sehen Sie eine Beispielausgabe.
    
    ```powershell
    PS C:\Offline docs\AzureVpnConfigurationScripts> .\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath ".\ServiceTags_Public_20200203.json" -ResourceGroupName "mytmarg3" -AzureDeploymentName "tmap2stestdeploy1" -NetworkRuleCollectionName "testnrc1" -Priority 115 -AppRuleCollectionName "testarc2"
        validating vpn deployment parameters
        Starting vpn deployment
        C:\Offline docs\AzureVpnConfigurationScripts\parameters-p2s.json
        C:\Offline docs\AzureVpnConfigurationScripts\template-p2s.json
        vpn deployment: tmap2stestdeploy1 started and status: Running
        Waiting for vpn deployment completion....
        ==== CUT ==================== CUT ==============
        Adding route 191.236.0.0/18 for AzureCloud.eastus
        Adding route 191.237.0.0/17 for AzureCloud.eastus
        Adding route 191.238.0.0/18 for AzureCloud.eastus
        Total Routes:294, Existing Routes: 74, New Routes Added: 220
        Additional routes getting added
    ```    

    > [!IMPORTANT]
    > - Die Ausführung des Skripts dauert etwa 90 Minuten. Stellen Sie sicher, dass Sie sich direkt vor Start des Skripts bei Ihrem Netzwerk anmelden.
    > - Wenn eine Sitzung mit dem Skript aus irgendeinem Grund fehlschlägt, stellen Sie sicher, dass Sie die Ressourcengruppe löschen, um alle darin erstellten Ressourcen zu löschen.
  
    
    Nach Abschluss des Skripts wird im Ordner, in dem sich das Skript befindet, ein Bereitstellungsprotokoll generiert.


## <a name="verify-the-azure-resources"></a>Überprüfen der Azure-Ressourcen

Nachdem Sie das Skript erfolgreich ausgeführt haben, überprüfen Sie, ob in Azure alle Ressourcen erstellt wurden. Wechseln Sie zur Ressourcengruppe, die Sie erstellt haben. Daraufhin sollten die folgenden Ressourcen angezeigt werden:

![Azure-Ressourcen](media/azure-stack-edge-mini-r-configure-vpn-powershell/script-resources.png)


<!--## Enable point-to-site in VPN gateway

1. In the Azure portal, go to the resource group and then select the virtual network gateway that you created in the earlier step.

    ![Azure virtual network gateway](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. Go to **Settings > Point-to-site configuration**. Select **Configure now**.

    ![Enable P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s.png)


3.  On the **Point-to-site configuration** blade:

    1. You'll add the client address pool. This pool is a range of private IP addresses that you specify. The clients that connect over a P2S VPN dynamically receive an IP address from this range. Use a private IP address range that does not overlap with the on-premises location that you connect from, or the VNet that you want to connect to. 
    2. You can select the tunnel type. For the VPN tunnel, you will use the IKEv2 protocol. 
    3. You will now define the type of authentication. Before Azure accepts a P2S VPN connection, the user has to be authenticated first. In this case, you authenticate using the native Azure certificate authentication. Set **Authentication type** to **Azure certificate**.
    4. You will now create and upload the certificates to Azure and your client. You will need to install a root certificate on your VPN gateway. The VPN gateway validates the client certificates when the P2S connection is being established by the client. The root certificate is required for the validation and must be uploaded to Azure.
    
        The clients trying to establish the P2S connection will have a client certificate that authenticates the connecting user.  To create these certificates, follow the steps in [Generate and export certificates for Point-to-Site using PowerShell](../vpn-gateway/vpn-gateway-certificates-point-to-site.md). 

        To install the root certificate, make sure the certificate is Base-64 encoded with a .cer extension. Open this certificate and copy the text in the certificate between ==BEGIN CERTIFICATE== and ==END CERTIFICATE== in one continuous line in the public certificate data under Root certificates.

        To upload the root certificates, follow the detailed steps in [Upload the root certificate public certificate data](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile).
    
    5. Save the configuration.

        ![Save P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s-config.png)-->

## <a name="download-phone-book-for-vpn-profile"></a>Herunterladen des Telefonbuchs für das VPN-Profil

In diesem Schritt wird das VPN-Profil für Ihr Gerät heruntergeladen.

1. Wechseln Sie im Azure-Portal zur Ressourcengruppe. Wählen Sie das Gateway des virtuellen Netzwerks aus, das Sie im vorherigen Schritt erstellt haben.

    ![Gateway für virtuelle Azure-Netzwerke](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. Wechseln Sie zu **Einstellungen > Punkt-zu-Standort-Konfiguration**. Wählen Sie **VPN-Client herunterladen** aus.

    ![Aktivieren der P2S-Konfiguration 1](media/azure-stack-edge-mini-r-configure-vpn-powershell/download-vpn-client.png)

2. Speichern Sie das gezippte Profil, und extrahieren Sie es auf dem Windows-Client.

    ![Aktivieren der P2S-Konfiguration 2](media/azure-stack-edge-mini-r-configure-vpn-powershell/save-extract-profile.png)

3. Wechseln Sie zum Ordner *WindowsAmd64*, und extrahieren Sie dann die `.exe`-Datei: *VpnClientSetupAmd64.exe*.

    ![Aktivieren der P2S-Konfiguration 3](media/azure-stack-edge-mini-r-configure-vpn-powershell/extract-exe.png)

3. Erstellen Sie einen temporären Pfad. Zum Beispiel:

    `C:\NewTemp\vnet\tmp`

4. Führen Sie PowerShell aus, und navigieren Sie zum Verzeichnis mit der `.exe`-Datei. Geben Sie Folgendes ein, um die `.exe`-Datei auszuführen:

    `.\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"`

5. Der temporäre Pfad weist neue Dateien auf. Hier ist eine Beispielausgabe:

    ```powershell
    
    PS C:\windows\system32> cd "C:\Users\Ase\Downloads\vngw5\WindowsAmd64"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> .\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> cd "C:\NewTemp\vnet"
    PS C:\NewTemp\vnet> ls .\tmp
    
        Directory: C:\NewTemp\vnet\tmp
    
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    -a----         2/6/2020   6:18 PM            947 8c670077-470b-421a-8dd8-8cedb4f2f08a.cer
    -a----         2/6/2020   6:18 PM            155 8c670077-470b-421a-8dd8-8cedb4f2f08a.cmp
    -a----         2/6/2020   6:18 PM           3564 8c670077-470b-421a-8dd8-8cedb4f2f08a.cms
    -a----         2/6/2020   6:18 PM          11535 8c670077-470b-421a-8dd8-8cedb4f2f08a.inf
    -a----         2/6/2020   6:18 PM           2285 8c670077-470b-421a-8dd8-8cedb4f2f08a.pbk
    -a----         2/6/2020   6:18 PM           5430 azurebox16.ico
    -a----         2/6/2020   6:18 PM           4286 azurebox32.ico
    -a----         2/6/2020   6:18 PM         138934 azurevpnbanner.bmp
    -a----         2/6/2020   6:18 PM          46064 cmroute.dll
    -a----         2/6/2020   6:18 PM            196 routes.txt
    
    PS C:\NewTemp\vnet>
    ```

6. Die *PBK*-Datei ist das Telefonbuch für das VPN-Profil. Es wird auf der lokalen Benutzeroberfläche verwendet.


## <a name="vpn-configuration-on-the-device"></a>VPN-Konfiguration auf dem Gerät

Führen Sie auf der lokalen Benutzeroberfläche Ihres Azure Stack Edge-Geräts die folgenden Schritte aus:

1. Wechseln Sie auf der lokalen Benutzeroberfläche zur Seite **VPN**. Wählen Sie unter „VPN-Status“ **Konfigurieren** aus.

    ![Konfigurieren von VPN 1](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-1.png)

2. Gehen Sie auf dem Blatt **VPN konfigurieren** wie folgt vor:
    
    1. Zeigen Sie in „Telefonbuchdatei hochladen (PBK)“ hochladen auf die PBK-Datei, die Sie in einem vorherigen Schritt erstellt haben.
    2. Geben Sie in „Upload public IP list config file“ (Konfigurationsdatei für Liste öffentlicher IP-Adressen hochladen) die JSON-Datei mit dem IP-Adressbereich des Azure-Rechenzentrums ein. Sie haben diese Datei in einem vorherigen Schritt von [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519) heruntergeladen.
    3. Wählen Sie als Region **eastus** und dann **Übernehmen** aus.

    ![Konfigurieren von VPN 2](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-2.png)

3. Geben Sie im Abschnitt **IP-Adressenbereiche, auf die nur über VPN zugegriffen werden soll** denIPv4-Bereich des VNET ein, den Sie für Ihr virtuelles Azure-Netzwerk gewählt hatten.

    ![Konfigurieren von VPN 3](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-3.png)

## <a name="verify-client-connection"></a>Überprüfen der Clientverbindung

1. Navigieren Sie im Azure-Portal zum VPN-Gateway.
2. Wechseln Sie zu **Einstellungen > Punkt-zu-Standort-Konfiguration**. Unter **Zugeordnete IP-Adressen** sollte die IP-Adresse Ihres Azure Stack Edge-Geräts gezeigt werden.

## <a name="validate-data-transfer-through-vpn"></a>Überprüfen der Datenübertragung über das VPN

Um zu bestätigen, dass das VPN funktioniert, kopieren Sie Daten in eine SMB-Freigabe. Führen Sie auf dem Azure Stack Edge-Gerät die Schritte unter [Hinzufügen einer Freigabe](azure-stack-edge-j-series-manage-shares.md#add-a-share) aus. 

1. Kopieren Sie eine Datei, z. B. „\data\pictures\waterfall.jpg“ in die SMB-Freigabe, die Sie auf dem Clientsystem eingebunden haben. 
2. Gehen Sie wie folgt vor, um zu überprüfen, ob die Daten das VPN durchlaufen, während sie kopiert werden:

    1. Navigieren Sie im Azure-Portal zum VPN-Gateway. 

    2. Wechseln Sie zu **Überwachung > Metriken**.

    3. Wählen Sie rechts als VPN-Gateway den **Bereich**, Gateway-P2S-Bandbreite als **Metrik** und als Durchschnitt **Aggregation** aus.

    4. Während die Daten kopiert werden, steigt die Bandbreitenauslastung, die wieder sinkt, sobald der Kopiervorgang abgeschlossen ist.

        ![Azure-VPN-Metriken](media/azure-stack-edge-mini-r-configure-vpn-powershell/vpn-metrics-1.png)

3. Vergewissern Sie sich, dass diese Datei in Ihrem Speicherkonto in der Cloud vorhanden sind.
 
## <a name="debug-issues"></a>Debuggen von Problemen

Verwenden Sie zum Debuggen von Problemen die folgenden Befehle:

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

Die Beispielausgabe wird unten gezeigt:


```azurepowershell
PS C:\Projects\TZL\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "tznvpnrg14_deployment" -ResourceGroupName "tznvpnrg14"


DeploymentName          : tznvpnrg14_deployment
ResourceGroupName       : tznvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 1/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     tznvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     tznvpnrg14_firewall
                          routeTables_routetable_name                  String                     tznvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     tznvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     tznvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     tznvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     tznvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     tznvpnrg14_connection
                          location                                     String                     East US
                          vnetIPv4AddressSpace                         String                     172.24.0.0/16
                          defaultSubnetIPv4AddressSpace                String                     172.24.0.0/24
                          firewallSubnetIPv4AddressSpace               String                     172.24.1.0/24
                          gatewaySubnetIPv4AddressSpace                String                     172.24.2.0/24
                          gatewaySubnetIPv4bgpPeeringAddress           String                     172.24.2.254
                          customerNetworkAddressSpace                  String                     10.0.0.0/18
                          customerPublicNetworkAddressSpace            String                     207.68.128.0/24
                          dbeIOTNetworkAddressSpace                    String                     10.139.218.0/24
                          azureVPNsharedKey                            String                     1234567890
                          dbE-Gateway-ipaddress                        String                     207.68.128.113

Outputs                 :
                          Name                     Type                       Value
                          =======================  =========================  ==========
                          virtualNetwork           Object                     {
                            "provisioningState": "Succeeded",
                            "resourceGuid": "dcf673d3-5c73-4764-b077-77125eda1303",
                            "addressSpace": {
                              "addressPrefixes": [
                                "172.24.0.0/16"
                              ]
================= CUT ============================= CUT ===========================
```


```
Get-AzResourceGroupDeploymentOperation -ResourceGroupName $ResourceGroupName -DeploymentName $AzureDeploymentName
```

## <a name="next-steps"></a>Nächste Schritte

[Konfigurieren von VPN auf der lokalen Benutzeroberfläche eines Azure Stack Edge-Geräts](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn)