---
title: Konfigurieren eines VPN auf Ihrem Azure Stack Edge Pro R-Gerät mithilfe von Azure PowerShell
description: Erfahren Sie, wie Sie ein VPN auf Ihrem Azure Stack Edge Pro R-Gerät mithilfe eines Azure PowerShell-Skripts zum Erstellen von Azure-Ressourcen konfigurieren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/23/2020
ms.author: alkohli
ms.openlocfilehash: 2139080367cdce9a5f018afab0970a7bd0e7504c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465452"
---
# <a name="configure-vpn-on-your-azure-stack-edge-pro-r-device-via-azure-powershell"></a>Konfigurieren eines VPN auf Ihrem Azure Stack Edge Pro R-Gerät mithilfe von Azure PowerShell

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

Die Option „VPN“ bietet eine zweite Verschlüsselungsebene für Daten, während diese über *TLS* von Ihrem Azure Stack Edge Pro R-Gerät nach Azure übertragen werden. Sie können ein VPN auf Ihrem Azure Stack Edge Pro R-Gerät über das Azure-Portal oder Azure PowerShell konfigurieren.

In diesem Artikel werden die zum Konfigurieren eines VPN auf Ihrem Azure Stack Edge Pro R-Gerät erforderlichen Schritte beschrieben, wobei die Konfiguration in der Cloud mithilfe von Azure PowerShell erstellt wird.

## <a name="about-vpn-setup"></a>Informationen zur VPN-Einrichtung

Eine standortübergreifende VPN-Verbindung besteht aus einem Azure-VPN-Gateway, einem lokalen VPN-Gerät und einem IPsec-S2S-VPN-Tunnel dazwischen. Der Workflow umfasst üblicherweise folgende Schritte:

- Erfüllen der Voraussetzungen
- Einrichten der erforderlichen Ressourcen in Azure
    - Erstellen und Konfigurieren eines Azure-VPN-Gateways (virtuelles Netzwerkgateway)
    - Erstellen und Konfigurieren eines lokalen Azure-Netzwerkgateways, das Ihr lokales Netzwerk und Ihr lokales VPN-Gerät darstellt
    - Erstellen und Konfigurieren einer Azure-VPN-Verbindung zwischen dem Azure-VPN-Gateway und dem lokalen Netzwerkgateway
    - Einrichten der Azure Firewall und Hinzufügen von Netzwerk- und App-Regeln
    - Erstellen einer Azure-Routingtabelle und Hinzufügen von Routen
- Einrichten des VPN auf der lokalen Webbenutzeroberfläche des Geräts. Sie konfigurieren das durch das lokale Netzwerkgateway dargestellte lokale VPN-Gerät, um den eigentlichen S2S-VPN-Tunnel mit dem Azure-VPN-Gateway einzurichten.

Details zu diesen Schritten finden Sie in den folgenden Abschnitten.

## <a name="configure-prerequisites"></a>Konfigurieren der Voraussetzungen

1. Sie benötigen Zugriff auf ein Azure Stack Edge Pro R-Gerät, das gemäß den Anweisungen unter [Installieren eines Azure Stack Edge Pro R-Geräts](azure-stack-edge-pro-r-deploy-install.md) installiert wurde. Dieses Gerät dient als lokales VPN-Gerät zum Herstellen der VPN-Verbindung mit Azure. 

2. Ihr VPN-Gerät sollte über eine statische öffentliche IP-Adresse (extern) verfügen. Dabei sollte es sich um keine NAT-Adresse handeln.

3. Sie benötigen Zugriff auf ein gültiges Azure-Abonnement, das für den Azure Stack Edge-Dienst in Azure aktiviert ist. Erstellen Sie im Rahmen dieses Abonnements eine entsprechende Ressource in Azure, um Ihr Azure Stack Edge Pro R-Gerät zu verwalten.  

4. Sie haben Zugriff auf einen Windows-Client, über den Sie auf das Azure Stack Edge Pro R-Gerät zugreifen. Mit diesem Client können Sie die Konfiguration in der Cloud programmgesteuert erstellen.

    1. Um die erforderliche PowerShell-Version auf Ihrem Windows-Client zu installieren, führen Sie die folgenden Befehle aus:

        ```azurepowershell
        Install-Module -Name Az -AllowClobber -Scope CurrentUser 
        Import-Module Az.Accounts
        ```
    2. Führen Sie die folgenden Befehle aus, um eine Verbindung mit Ihrem Azure-Konto und -Abonnement herzustellen:

        ```azurepowershell
        Connect-AzAccount 
        Set-AzContext -Subscription "<Your subscription name>"
        ```
        Geben Sie den Namen des Azure-Abonnements an, das Sie mit Ihrem Azure Stack Edge Pro R-Gerät zum Konfigurieren des VPN verwenden.

    3. [Laden Sie das Skript herunter](https://aka.ms/ase-vpn-deployment), das zum Erstellen der Konfiguration in der Cloud erforderlich ist. Das Skript führt Folgendes aus:
        
        - Erstellen eines virtuelles Azure-Netzwerks und der folgenden Subnetze: *GatewaySubnet* und *AzureFirewallSubnet*
        - Erstellen und Konfigurieren eines Azure-VPN-Gateways
        - Erstellen und Konfigurieren eines lokalen Azure-Netzwerkgateways
        - Erstellen und Konfigurieren einer Azure-VPN-Verbindung zwischen dem Azure-VPN-Gateway und dem lokalen Netzwerkgateway
        - Erstellen einer Azure Firewall und Hinzufügen von Netzwerk- und App-Regeln
        - Erstellen einer Azure-Routingtabelle und Hinzufügen von Routen


## <a name="use-the-script"></a>Verwenden des Skripts

Zuerst ändern Sie die Datei `parameters.json`, indem Sie Ihre Parameter eingeben. Als Nächstes führen Sie das Skript unter Verwendung der geänderten JSON-Datei aus.

Jeder dieser Schritte wird in den folgenden Abschnitten ausführlich erläutert.

### <a name="download-service-tags-file"></a>Herunterladen der Datei mit den Diensttags

Möglicherweise ist die Datei `ServiceTags.json` bereits in dem Ordner enthalten, in den Sie das Skript heruntergeladen haben. Andernfalls können Sie die Datei mit den Diensttags herunterladen.

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>Ändern der Datei „parameters“

Der erste Schritt besteht darin, die Datei `parameters.json` zu ändern und die Änderungen zu speichern. 


Geben Sie für die Azure-Ressourcen, die Sie erstellen, die folgenden Namen an:

|Parametername  |BESCHREIBUNG  |
|---------|---------|
|virtualNetworks_vnet_name    | Name der Azure Virtual Network-Instanz        |
|azureFirewalls_firewall_name     | Name der Azure Firewall-Instanz        |
|routeTables_routetable_name     | Name der Azure-Routingtabelle        |
|publicIPAddresses_VNGW_public_ip_name     | Name der öffentlichen IP-Adresse Ihres virtuellen Netzwerkgateways       |
|virtualNetworkGateways_VNGW_name    | Name des Azure-VPN-Gateways (virtuelles Netzwerkgateway)        |
|publicIPAddresses_firewall_public_ip_name     | Name der öffentlichen IP-Adresse Ihrer Azure Firewall-Instanz         |
|localNetworkGateways_LNGW_name    |Name des lokalen Azure-Netzwerkgateways          |
|connections_vngw_lngw_name    | Name der Azure-VPN-Verbindung. Dies ist die Verbindung zwischen Ihrem virtuellen Netzwerkgateway und dem lokalen Netzwerkgateway.       |
|location     |Dies ist die Region, in der Sie Ihr virtuelles Netzwerk erstellen möchten. Wählen Sie die Region aus, die Ihrem Gerät zugeordnet ist.         |

Die folgenden IP-Adressen und Adressräume gehören zu den erstellten Azure-Ressourcen, einschließlich des virtuellen Netzwerks und der zugehörigen Subnetze („Default“, „Firewall“, „GatewaySubnet“).

|Parametername  |BESCHREIBUNG  |
|---------|---------|
|VnetIPv4AddressSpace    | Dies ist der Ihrem virtuellen Netzwerk zugeordnete Adressraum.       |
|DefaultSubnetIPv4AddressSpace    |Dies ist der dem Subnetz `Default` für Ihr virtuelles Netzwerk zugeordnete Adressraum.         |
|FirewallSubnetIPv4AddressSpace    |Dies ist der dem Subnetz `Firewall` für Ihr virtuelles Netzwerk zugeordnete Adressraum.          |
|GatewaySubnetIPv4AddressSpace    |Dies ist der Adressraum, der dem `GatewaySubnet` für Ihr virtuelles Netzwerk zugeordnet ist.          |
|GatewaySubnetIPv4bgpPeeringAddress    | Dies ist die IP-Adresse, die für die BGP-Kommunikation reserviert ist und auf dem Adressraum basiert, der dem `GatewaySubnet` für Ihr virtuelles Netzwerk zugeordnet ist.          |

Die folgenden IP-Adressen und Adressräume gehören zum lokalen Netzwerk (in dem Ihr Azure Stack Edge Pro R-Gerät bereitgestellt wird).

|Parametername  |BESCHREIBUNG  |
|---------|---------|
|CustomerNetworkAddressSpace    |  Dies ist der Adressraum für Ihre private IP-Adresse.       |
|CustomerPublicNetworkAddressSpace    |  Dies ist der Adressraum für Ihre öffentliche IP-Adresse.       |
|DbeIOTNetworkAddressSpace    |Diese IP-Adresse wird vom IoT-Dienst reserviert. Dieser Parameter darf nicht geändert werden.        |
|AzureVPNsharedKey    |Dieser gemeinsam verwendete Schlüssel wird während der Erstellung der Azure-VPN-Verbindungsressource verwendet. Dieser Schlüssel wird auch als gemeinsamer geheimer VPN-Schlüssel angegeben, wenn das VPN über die lokale Webbenutzeroberfläche konfiguriert wird.         |
|DBE-Gateway-ipaddress   | Die öffentliche IP-Adresse für Ihr Azure Stack Edge Pro R-Gerät. Diese ist möglicherweise nicht bekannt, und Sie können das Skript mit einer Platzhalter-IP-Adresse ausführen. Bearbeiten Sie später das lokale Netzwerkgateway mit der tatsächlichen IP-Adresse.     |

#### <a name="caveats-to-keep-in-mind"></a>Einschränkungen, die Sie beachten sollten:

- Sie verfügen nicht über die IP-Adresse des Azure Stack Edge Pro R-Geräts. Sie können eine Platzhalter-IP-Adresse verwenden, um Ihre Ressource zu erstellen, und später das lokale Azure-Netzwerkgateway ändern, um die tatsächliche IP-Adresse des Geräts und den Adressraum des lokalen Netzwerks für das Gerät zuzuweisen.
- Verwenden Sie basierend auf der Anweisung der IETF zu Internet Assigned Numbers Authority (IANA) ein beliebiges Subnetz von 172.16.x.y bis 172.24.z.a. Wir reservieren die 172.24-IPv4-Adressbereiche für das Azure-Netzwerk.

### <a name="run-the-script"></a>Führen Sie das Skript aus.

Führen Sie die folgenden Schritte aus, um die geänderte Datei `parameters.json` zu verwenden und das Skript zum Erstellen von Azure-Ressourcen auszuführen.

1. Führen Sie PowerShell als Administrator aus.

2. Wechseln Sie zum Verzeichnis mit dem Skript.

3. Führen Sie das Skript aus.

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    Nachfolgend sehen Sie eine Beispielausgabe.

    `.\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "ServiceTags_Public_20191216.json"  -ResourceGroupName "devtestrg4" -AzureDeploymentName "dbetestdeployment20" -NetworkRuleCollectionName "testnrc20" -Priority 115 -AppRuleCollectionName "testarc20"`

    Die Ausführung des Skripts dauert etwa 90 Minuten. Nach Abschluss des Skripts wird im Ordner, in dem sich das Skript befindet, ein Bereitstellungsprotokoll generiert.


## <a name="verify-the-azure-resources"></a>Überprüfen der Azure-Ressourcen

Nachdem Sie das Skript erfolgreich ausgeführt haben, überprüfen Sie, ob in Azure alle Ressourcen erstellt wurden.

Im nächsten Schritt konfigurieren Sie das VPN auf der lokalen Webbenutzeroberfläche Ihres Geräts.


## <a name="vpn-configuration-on-the-device"></a>VPN-Konfiguration auf dem Gerät

[!INCLUDE [azure-stack-edge-gateway-configure-vpn-local-ui](../../includes/azure-stack-edge-gateway-configure-vpn-local-ui.md)]


## <a name="verify-vpn"></a>Überprüfen des VPN

[!INCLUDE [azure-stack-edge-gateway-verify-vpn](../../includes/azure-stack-edge-gateway-verify-vpn.md)]

## <a name="validate-data-transfer-through-vpn"></a>Überprüfen der Datenübertragung über das VPN

Um zu bestätigen, dass das VPN funktioniert, kopieren Sie Daten in eine SMB-Freigabe. Führen Sie auf dem Azure Stack Edge Pro R-Gerät die Schritte unter [Hinzufügen einer Freigabe](azure-stack-edge-j-series-manage-shares.md#add-a-share) aus. 

1. Kopieren Sie eine Datei, z. B. „\data\pictures\waterfall.jpg“, in die SMB-Freigabe, die Sie auf dem Clientsystem bereitgestellt haben. 
2. Vergewissern Sie sich, dass diese Datei in Ihrem Speicherkonto in der Cloud vorhanden sind.

So überprüfen Sie, ob die Daten über das VPN übertragen werden:

1. Öffnen Sie die Verbindungsressource in der Ressourcengruppe. 

2. Überprüfen Sie den Wert für eingehende und ausgehende Daten.
 
3. Öffnen Sie das virtuelle Netzwerkgateway in Ihrer Ressourcengruppe. Zeigen Sie die Diagramme für **Eingehende Tunneldaten gesamt** und **Ausgehende Tunneldaten gesamt** an.
 
## <a name="debug-issues"></a>Debuggen von Problemen

Verwenden Sie zum Debuggen von Problemen die folgenden Befehle:

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

Die Beispielausgabe wird unten gezeigt:


```azurepowershell
PS C:\Projects\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "aseprorvpnrg14_deployment" -ResourceGroupName "aseprorvpnrg14"


DeploymentName          : aseprorvpnrg14_deployment
ResourceGroupName       : aseprorvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 10/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     aseprorvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     aseprorvpnrg14_firewall
                          routeTables_routetable_name                  String                     aseprorvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     aseprorvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     aseprorvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     aseprorvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     aseprorvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     aseprorvpnrg14_connection
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

[Konfigurieren eines VPN über die lokale Benutzeroberfläche eines Azure Stack Edge Pro R-Geräts](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn)
