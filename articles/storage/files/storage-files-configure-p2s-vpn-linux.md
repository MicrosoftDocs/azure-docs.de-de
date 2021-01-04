---
title: Konfigurieren eines P2S-VPN (Point-to-Site) unter Linux zur Verwendung mit Azure Files | Microsoft-Dokumentation
description: Konfigurieren eines P2S-VPN (Point-to-Site) unter Linux zur Verwendung mit Azure Files
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 74422318718e318a00d7bd7ebaf8e4093ef75aa6
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629273"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>Konfigurieren eines P2S-VPN (Point-to-Site) unter Linux zur Verwendung mit Azure Files
Sie können eine P2S-VPN-Verbindung (Point-to-Site) verwenden, um Ihre Azure-Dateifreigaben außerhalb von Azure über SMB einzubinden, ohne Port 445 zu öffnen. Eine P2S-VPN-Verbindung ist eine VPN-Verbindung zwischen Azure und einem einzelnen Client. Um eine P2S-VPN-Verbindung mit Azure Files zu verwenden, muss für jeden Client, der eine Verbindung herstellen möchte, eine P2S-VPN-Verbindung konfiguriert werden. Wenn Sie über viele Clients verfügen, die sich über Ihr lokales Netzwerk mit Ihren Azure-Dateifreigaben verbinden müssen, können Sie anstelle einer P2S-Verbindung für jeden Client eine S2S-VPN-Verbindung (Site-to-Site) verwenden. Weitere Informationen finden Sie unter [Konfigurieren eines S2S-VPN (Site-to-Site) zur Verwendung mit Azure Files](storage-files-configure-s2s-vpn.md).

Es wird dringend empfohlen, vor der Lektüre des vorliegenden Artikels den Artikel [Azure Files – Überlegungen zum Netzwerkbetrieb](storage-files-networking-overview.md) zu lesen, der eine umfassende Erläuterung der für Azure Files verfügbaren Netzwerkoptionen enthält.

Der Artikel beschreibt die Schritte zur Konfiguration eines P2S-VPN unter Linux, um Azure-Dateifreigaben direkt lokal einzubinden. Wenn Sie den Datenverkehr zur Azure-Dateisynchronisierung über ein VPN leiten möchten, lesen Sie den Artikel [Konfigurieren der Proxy- und Firewalleinstellungen der Dateisynchronisierung](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Voraussetzungen
- Die neueste Version der Azure CLI. Informationen zum Installieren der Azure CLI finden Sie unter [Installieren der Azure PowerShell CLI](/cli/azure/install-azure-cli) im Abschnitt zu Ihrem Betriebssystem. Wenn Sie lieber mit dem Azure PowerShell-Modul unter Linux arbeiten möchten, können Sie dies tun, die folgenden Anweisungen beziehen sich jedoch auf die Azure CLI.

- Eine Azure-Dateifreigabe, die Sie lokal einbinden möchten. Azure-Dateifreigaben werden in Speicherkonten bereitgestellt. Hierbei handelt es sich um Verwaltungskonstrukte, die einen gemeinsam genutzten Pool mit Speicherplatz darstellen, in dem Sie mehrere Dateifreigaben sowie weitere Speicherressourcen wie Blobcontainer oder Warteschlangen bereitstellen können. Weitere Informationen zum Bereitstellen von Azure-Dateifreigaben und Speicherkonten finden Sie unter [Erstellen einer Azure-Dateifreigabe](storage-how-to-create-file-share.md).

- Ein privater Endpunkt für das Speicherkonto mit der Azure-Dateifreigabe, die Sie lokal bereitstellen möchten. Weitere Informationen zum Erstellen eines privaten Endpunkts finden Sie unter [Konfigurieren von Azure Files-Netzwerkendpunkten](storage-files-networking-endpoints.md?tabs=azure-cli). 

## <a name="install-required-software"></a>Installieren der erforderlichen Software
Das Azure-Gateway für virtuelle Netzwerke kann VPN-Verbindungen über verschiedene VPN-Protokolle bereitstellen, darunter z. B. IPsec und OpenVPN. Dieser Leitfaden zeigt die Verwendung von IPsec und verwendet das strongSwan-Paket, um die Unterstützung unter Linux zu gewährleisten. 

> Verifiziert mit Ubuntu 18.10.

```bash
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>Bereitstellen eines virtuellen Netzwerks 
Um von einer lokalen Umgebung aus über ein P2S-VPN auf Ihre Azure-Dateifreigabe und andere Azure-Ressourcen zuzugreifen, müssen Sie ein virtuelles Netzwerk (VNET) erstellen. Die automatisch erstellte P2S-VPN-Verbindung ist eine Brücke zwischen Ihrem lokalen Linux-Computer und diesem virtuellen Azure-Netzwerk.

Das folgende Skript erstellt ein virtuelles Azure-Netzwerk mit drei Subnetzen: ein Subnetz für den Dienstendpunkt Ihres Speicherkontos, ein Subnetz für den privaten Endpunkt Ihres Speicherkontos (dieser wird für den Zugriff auf das lokale Speicherkonto ohne benutzerdefiniertes Routing für die öffentliche IP-Adresse benötigt, die sich ändern kann), und ein Subnetz für das Gateway für virtuelle Netzwerke, das den VPN-Dienst bereitstellt. 

Denken Sie daran, `<region>`, `<resource-group>` und `<desired-vnet-name>` durch die entsprechenden Werte für Ihre Umgebung zu ersetzen.

```bash
region="<region>"
resourceGroupName="<resource-group>"
virtualNetworkName="<desired-vnet-name>"

virtualNetwork=$(az network vnet create \
    --resource-group $resourceGroupName \
    --name $virtualNetworkName \
    --location $region \
    --address-prefixes "192.168.0.0/16" \
    --query "newVNet.id" | tr -d '"')

serviceEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "ServiceEndpointSubnet" \
    --address-prefixes "192.168.0.0/24" \
    --service-endpoints "Microsoft.Storage" \
    --query "id" | tr -d '"')

privateEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "PrivateEndpointSubnet" \
    --address-prefixes "192.168.1.0/24" \
    --query "id" | tr -d '"')

gatewaySubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "GatewaySubnet" \
    --address-prefixes "192.168.2.0/24" \
    --query "id" | tr -d '"')
```

## <a name="create-certificates-for-vpn-authentication"></a>Erstellen von Zertifikaten für die VPN-Authentifizierung
Damit VPN-Verbindungen von Ihren lokalen Linux-Computern für den Zugriff auf Ihr virtuelles Netzwerk authentifiziert werden können, müssen Sie zwei Zertifikate erstellen: ein Stammzertifikat, das für das VM-Gateway bereitgestellt wird, und ein Clientzertifikat, das mit dem Stammzertifikat signiert wird. Das folgende Skript erstellt die erforderlichen Zertifikate.

```bash
rootCertName="P2SRootCert"
username="client"
password="1234"

mkdir temp
cd temp

sudo ipsec pki --gen --outform pem > rootKey.pem
sudo ipsec pki --self --in rootKey.pem --dn "CN=$rootCertName" --ca --outform pem > rootCert.pem

rootCertificate=$(openssl x509 -in rootCert.pem -outform der | base64 -w0 ; echo)

sudo ipsec pki --gen --size 4096 --outform pem > "clientKey.pem"
sudo ipsec pki --pub --in "clientKey.pem" | \
    sudo ipsec pki \
        --issue \
        --cacert rootCert.pem \
        --cakey rootKey.pem \
        --dn "CN=$username" \
        --san $username \
        --flag clientAuth \
        --outform pem > "clientCert.pem"

openssl pkcs12 -in "clientCert.pem" -inkey "clientKey.pem" -certfile rootCert.pem -export -out "client.p12" -password "pass:$password"
```

## <a name="deploy-virtual-network-gateway"></a>Bereitstellen eines Gateways für virtuelle Netzwerke
Das Azure-Gateway für virtuelle Netzwerke ist der Dienst, mit dem sich Ihre lokalen Linux-Computer verbinden. Zur Bereitstellung dieses Diensts werden zwei grundlegende Komponenten benötigt: eine öffentliche IP-Adresse, die das Gateway gegenüber Ihren Clients identifiziert (unabhängig von deren Standort), und ein zuvor erstelltes Stammzertifikat für die Authentifizierung Ihrer Clients.

Denken Sie daran, `<desired-vpn-name-here>` durch den Namen zu ersetzen, den Sie für diese Ressourcen verwenden möchten.

> [!Note]  
> Die Bereitstellung eines Azure-Gateways für virtuelle Netzwerke kann bis zu 45 Minuten dauern. Während der Bereitstellung dieser Ressource wird dieses Bash-Skript blockiert, damit die Bereitstellung abgeschlossen werden kann.
>
> P2S IKEv2-/OpenVPN-Verbindungen werden für die SKU **Basic** nicht unterstützt. In diesem Skript wird die SKU **VpnGw1** entsprechend für das Gateway für virtuelle Netzwerke verwendet.

```bash
vpnName="<desired-vpn-name-here>"
publicIpAddressName="$vpnName-PublicIP"

publicIpAddress=$(az network public-ip create \
    --resource-group $resourceGroupName \
    --name $publicIpAddressName \
    --location $region \
    --sku "Basic" \
    --allocation-method "Dynamic" \
    --query "publicIp.id" | tr -d '"')

az network vnet-gateway create \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --vnet $virtualNetworkName \
    --public-ip-addresses $publicIpAddress \
    --location $region \
    --sku "VpnGw1" \
    --gateway-typ "Vpn" \
    --vpn-type "RouteBased" \
    --address-prefixes "172.16.201.0/24" \
    --client-protocol "IkeV2" > /dev/null

az network vnet-gateway root-cert create \
    --resource-group $resourceGroupName \
    --gateway-name $vpnName \
    --name $rootCertName \
    --public-cert-data $rootCertificate \
    --output none
```

## <a name="configure-the-vpn-client"></a>Konfigurieren des VPN-Clients
Das Azure-Gateway für virtuelle Netzwerke erstellt ein herunterladbares Paket mit Konfigurationsdateien, die für die Initialisierung der VPN-Verbindung auf Ihrem lokalen Linux-Computer erforderlich sind. Das folgende Skript platziert die von Ihnen erstellten Zertifikate am richtigen Standort und konfiguriert die `ipsec.conf`-Datei mit den korrekten Werten aus der Konfigurationsdatei im heruntergeladenen Paket.

```bash
vpnClient=$(az network vnet-gateway vpn-client generate \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --authentication-method EAPTLS | tr -d '"')

curl $vpnClient --output vpnClient.zip
unzip vpnClient.zip

vpnServer=$(xmllint --xpath "string(/VpnProfile/VpnServer)" Generic/VpnSettings.xml)
vpnType=$(xmllint --xpath "string(/VpnProfile/VpnType)" Generic/VpnSettings.xml | tr '[:upper:]' '[:lower:]')
routes=$(xmllint --xpath "string(/VpnProfile/Routes)" Generic/VpnSettings.xml)

sudo cp "${installDir}ipsec.conf" "${installDir}ipsec.conf.backup"
sudo cp "Generic/VpnServerRoot.cer" "${installDir}ipsec.d/cacerts"
sudo cp "${username}.p12" "${installDir}ipsec.d/private" 

echo -e "\nconn $virtualNetworkName" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tkeyexchange=$vpnType" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\ttype=tunnel" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftfirewall=yes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleft=%any" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftauth=eap-tls" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftid=%client" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tright=$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightid=%$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightsubnet=$routes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftsourceip=%config" | sudo tee -a "${installDir}ipsec.conf" > /dev/null 
echo -e "\tauto=add" | sudo tee -a "${installDir}ipsec.conf" > /dev/null

echo ": P12 client.p12 '$password'" | sudo tee -a "${installDir}ipsec.secrets" > /dev/null

sudo ipsec restart
sudo ipsec up $virtualNetworkName 
```

## <a name="mount-azure-file-share"></a>Einbinden einer Azure-Dateifreigabe
Nun, da Sie Ihr P2S-VPN eingerichtet haben, können Sie Ihre Azure-Dateifreigabe einbinden. Im folgenden Beispiel wird die Freigabe nicht dauerhaft eingebunden. Informationen für eine dauerhafte Einbindung finden Sie unter [Verwenden einer Azure-Dateifreigabe mit Linux](storage-how-to-use-files-linux.md). 

```bash
fileShareName="myshare"

mntPath="/mnt/$storageAccountName/$fileShareName"
sudo mkdir -p $mntPath

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

smbPath="//$storageAccountPrivateIP/$fileShareName"
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

## <a name="see-also"></a>Weitere Informationen
- [Azure Files – Überlegungen zum Netzwerkbetrieb](storage-files-networking-overview.md)
- [Konfigurieren eines P2S-VPN (Point-to-Site) unter Windows zur Verwendung mit Azure Files](storage-files-configure-p2s-vpn-windows.md)
- [Konfigurieren eines S2S-VPN (Site-to-Site) zur Verwendung mit Azure Files](storage-files-configure-s2s-vpn.md)