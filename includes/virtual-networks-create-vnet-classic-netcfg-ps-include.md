---
title: include file
description: include file
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: bda289e73b9a782cd56c0c94b8f53e8002b1ccf4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178008"
---
## <a name="how-to-create-a-virtual-network-using-a-network-config-file-from-powershell"></a>Erstellen eines virtuellen Netzwerks mithilfe einer Netzwerkkonfigurationsdatei von PowerShell
Azure verwendet eine XML-Datei, um alle für ein Abonnement verfügbaren virtuellen Netzwerke zu definieren. Sie können diese Datei herunterladen und bearbeiten, um vorhandene virtuelle Netzwerke zu ändern oder zu löschen und neue Netzwerke zu erstellen. In diesem Tutorial erfahren Sie, wie Sie diese Datei, die auch als Netzwerkkonfigurationsdatei oder netcfg-Datei bezeichnet wird, herunterladen und bearbeiten können, um ein neues virtuelles Netzwerk zu erstellen. Weitere Informationen zur Netzwerkkonfigurationsdatei finden Sie im [Konfigurationsschema für virtuelle Azure-Netzwerke](https://msdn.microsoft.com/library/azure/jj157100.aspx).

Um ein virtuelles Netzwerk mit PowerShell und einer netcfg-Datei zu erstellen, müssen Sie die folgenden Schritte ausführen:

1. Wenn Sie Azure PowerShell zum ersten Mal verwenden, folgen Sie den Anweisungen unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azureps-cmdlets-docs). Melden Sie sich dann bei Azure an, und wählen Sie Ihr Abonnement aus.
2. Verwenden Sie in der Azure PowerShell-Konsole das **Get-AzureVnetConfig**-Cmdlet, um die Netzwerkkonfigurationsdatei in ein Verzeichnis auf Ihrem Computer herunterzuladen, indem Sie den folgenden Befehl ausführen: 
   
   ```powershell
   Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
   ```
   
   Erwartete Ausgabe:
  
      ```
      XMLConfiguration                                                                                                     
      ----------------                                                                                                     
      <?xml version="1.0" encoding="utf-8"?>...
      ```

3. Öffnen Sie die in Schritt 2 gespeicherte Datei mit einem beliebigen XML- oder Text-Editor, und suchen Sie nach dem **\<VirtualNetworkSites>** -Element. Wenn Sie bereits Netzwerke erstellt haben, wird für jedes Netzwerk ein eigenes **\<VirtualNetworkSites>** -Element angezeigt.
4. Um das in diesem Szenario beschriebene virtuelle Netzwerk zu erstellen, fügen Sie den folgenden XML-Code direkt unter dem **\<VirtualNetworkSites>** -Element hinzu:

   ```xml
         <?xml version="1.0" encoding="utf-8"?>
         <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
           <VirtualNetworkConfiguration>
             <VirtualNetworkSites>
                 <VirtualNetworkSite name="TestVNet" Location="East US">
                   <AddressSpace>
                     <AddressPrefix>192.168.0.0/16</AddressPrefix>
                   </AddressSpace>
                   <Subnets>
                     <Subnet name="FrontEnd">
                       <AddressPrefix>192.168.1.0/24</AddressPrefix>
                     </Subnet>
                     <Subnet name="BackEnd">
                       <AddressPrefix>192.168.2.0/24</AddressPrefix>
                     </Subnet>
                   </Subnets>
                 </VirtualNetworkSite>
             </VirtualNetworkSites>
           </VirtualNetworkConfiguration>
         </NetworkConfiguration>
   ```
   
5. Speichern Sie die Netzwerkkonfigurationsdatei.
6. Verwenden Sie in der Azure PowerShell-Konsole das **Set-AzureVnetConfig**-Cmdlet, um die Netzwerkkonfigurationsdatei hochzuladen, indem Sie den folgenden Befehl ausführen: 
   
   ```powershell
   Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
   ```
   
   Zurückgegebene Ausgabe:
   
      ```
      OperationDescription OperationId                          OperationStatus
      -------------------- -----------                          ---------------
      Set-AzureVNetConfig  <Id>                                 Succeeded 
      ```
   
   Wenn **OperationStatus** in der zurückgegebenen Ausgabe nicht den Wert *Erfolgreich* aufweist, überprüfen Sie die XML-Datei auf Fehler, und wiederholen Sie Schritt 6.

7. Verwenden Sie in der Azure PowerShell-Konsole das **Get-AzureVnetSite**-Cmdlet, um zu überprüfen, ob das neue Netzwerk hinzugefügt wurde, indem Sie den folgenden Befehl ausführen: 

   ```powershell
   Get-AzureVNetSite -VNetName TestVNet
   ```
   
   Die zurückgegebene (gekürzte) Ausgabe enthält den folgenden Text:
  
      ```
      AddressSpacePrefixes : {192.168.0.0/16}
      Location             : Central US
      Name                 : TestVNet
      State                : Created
      Subnets              : {FrontEnd, BackEnd}
      OperationDescription : Get-AzureVNetSite
      OperationStatus      : Succeeded
      ```
