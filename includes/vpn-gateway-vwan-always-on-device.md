---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 34e841a5f17d589c4fbef54a4a8674a99ac6c640
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026918"
---
Um einen Gerätetunnel einzurichten, müssen die folgenden Anforderungen erfüllt werden:

* Das Gerät muss ein in die Domäne eingebundener Computer mit Windows 10 Enterprise oder Education ab Version 1809 sein.
* Der Tunnel ist nur für die unter Windows integrierte VPN-Lösung konfigurierbar und wird mit IKEv2 mit Computerzertifikatsauthentifizierung eingerichtet.
* Pro Gerät kann nur ein Gerätetunnel konfiguriert werden.

1. Installieren Sie Clientzertifikate auf dem Windows 10-Client mithilfe des Artikels [Point-to-Site-VPN-Client](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md). Das Zertifikat muss sich im Speicher „Lokaler Computer“ befinden.
1. Verwenden Sie [diese Anweisungen](/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration), um ein VPN-Profil zu erstellen und den Gerätetunnel im Kontext des lokalen Systemkontos zu konfigurieren.

### <a name="configuration-example-for-device-tunnel"></a>Beispiel für die Konfiguration eines Gerätetunnels

Nachdem Sie das virtuelle Netzwerkgateway konfiguriert und das Clientzertifikat im Speicher „Lokaler Computer“ auf dem Windows 10-Client installiert haben, verwenden Sie die folgenden Beispiele, um einen Clientgerätetunnel zu konfigurieren:

1. Kopieren Sie den folgenden Text, und speichern Sie ihn als Datei **devicecert.ps1**.

   ```
   Param(
   [string]$xmlFilePath,
   [string]$ProfileName
   )

   $a = Test-Path $xmlFilePath
   echo $a

   $ProfileXML = Get-Content $xmlFilePath

   echo $XML

   $ProfileNameEscaped = $ProfileName -replace ' ', '%20'

   $Version = 201606090004

   $ProfileXML = $ProfileXML -replace '<', '&lt;'
   $ProfileXML = $ProfileXML -replace '>', '&gt;'
   $ProfileXML = $ProfileXML -replace '"', '&quot;'

   $nodeCSPURI = './Vendor/MSFT/VPNv2'
   $namespaceName = "root\cimv2\mdm\dmmap"
   $className = "MDM_VPNv2_01"

   $session = New-CimSession

   try
   {
   $newInstance = New-Object Microsoft.Management.Infrastructure.CimInstance $className, $namespaceName
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ParentID", "$nodeCSPURI", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("InstanceID", "$ProfileNameEscaped", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ProfileXML", "$ProfileXML", 'String', 'Property')
   $newInstance.CimInstanceProperties.Add($property)

   $session.CreateInstance($namespaceName, $newInstance)
   $Message = "Created $ProfileName profile."
   Write-Host "$Message"
   }
   catch [Exception]
   {
   $Message = "Unable to create $ProfileName profile: $_"
   Write-Host "$Message"
   exit
   }
   $Message = "Complete."
   Write-Host "$Message"
   ```
1. Kopieren Sie den folgenden Text, und speichern Sie ihn als Datei _*_VPNProfile.xml_*_ in demselben Ordner wie die Datei „devicecert.ps1“. Bearbeiten Sie den folgenden Text, um ihn an Ihre Umgebung anzupassen.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers> <= Can be found in the VpnSettings.xml in the downloaded profile zip file`
   * `<Address>192.168.3.5</Address> <= IP of resource in the vnet or the vnet address space`
   * `<Address>192.168.3.4</Address> <= IP of resource in the vnet or the vnet address space`

   ```
   <VPNProfile>  
     <NativeProfile>  
   <Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  
   <NativeProtocolType>IKEv2</NativeProtocolType>  
   <Authentication>  
     <MachineMethod>Certificate</MachineMethod>  
   </Authentication>  
   <RoutingPolicyType>SplitTunnel</RoutingPolicyType>  
    <!-- disable the addition of a class based route for the assigned IP address on the VPN interface -->
   <DisableClassBasedDefaultRoute>true</DisableClassBasedDefaultRoute>  
     </NativeProfile> 
     <!-- use host routes(/32) to prevent routing conflicts -->  
     <Route>  
   <Address>192.168.3.5</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
     <Route>  
   <Address>192.168.3.4</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
   <!-- need to specify always on = true --> 
     <AlwaysOn>true</AlwaysOn> 
   <!-- new node to specify that this is a device tunnel -->  
    <DeviceTunnel>true</DeviceTunnel>
   <!--new node to register client IP address in DNS to enable manage out -->
   <RegisterDNS>true</RegisterDNS>
   </VPNProfile>
   ```
1. Laden Sie **PsExec** von [Sysinternals](/sysinternals/downloads/psexec) herunter, und extrahieren Sie die Dateien in **C:\PSTools**.
1. Starten Sie PowerShell über eine Administrator-CMD-Eingabeaufforderung, indem Sie Folgendes ausführen:

   ```
   PsExec.exe Powershell for 32-bit Windows
   PsExec64.exe Powershell for 64-bit Windows
   ```

   ![Der Screenshot zeigt ein Eingabeaufforderungsfenster mit einem Befehl zum Starten der 64-Bit-Version von PowerShell.](./media/vpn-gateway-vwan-always-on-device/powershell.png)
1. Wechseln Sie in PowerShell zum Ordner, in dem sich die **devicecert.ps1**- und **VPNProfile.xml**-Datei befinden, und führen Sie den folgenden Befehl aus:

   ```powershell
   .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![Der Screenshot zeigt ein PowerShell-Fenster, in dem „MachineCertTest“ mithilfe des Skripts „devicesert“ ausgeführt wurde.](./media/vpn-gateway-vwan-always-on-device/machinecerttest.png)
1. Führen Sie **rasphone** aus.

   ![Der Screenshot zeigt das Dialogfeld „Ausführen“ mit ausgewähltem „rasphone“.](./media/vpn-gateway-vwan-always-on-device/rasphone.png)
1. Suchen Sie den Eintrag **MachineCertTest**, und klicken Sie auf **Verbinden**.

   ![Der Screenshot zeigt das Dialogfeld „Netzwerkverbindungen“ mit ausgewähltem „MachineCertTest“ und der Schaltfläche „Verbinden“.](./media/vpn-gateway-vwan-always-on-device/connect.png)
1. Wenn die Verbindung erfolgreich hergestellt wurde, starten Sie den Computer neu. Der Tunnel wird automatisch eine Verbindung herstellen.