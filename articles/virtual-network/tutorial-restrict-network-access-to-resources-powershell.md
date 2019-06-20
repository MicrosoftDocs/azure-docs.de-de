---
title: 'Einschränken des Netzwerkzugriffs auf PaaS-Ressourcen: Azure PowerShell | Microsoft-Dokumentation'
description: In diesem Artikel erfahren Sie, wie Sie mithilfe von Azure PowerShell den Netzwerkzugriff auf Azure-Ressourcen wie Azure Storage und Azure SQL-Datenbank mit VNET-Dienstendpunkten einschränken können.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: b76256ef70b85df0c504427179518d175f08b645
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66727669"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-powershell"></a>Einschränken des Netzwerkzugriffs auf PaaS-Ressourcen mit VNET-Dienstendpunkten mithilfe von PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

VNET-Dienstendpunkte ermöglichen es Ihnen, den Netzwerkzugriff auf einige Azure-Dienstressourcen auf ein Subnetz eines virtuellen Netzwerks einzuschränken. Sie können auch den Internetzugriff auf die Ressourcen entfernen. Dienstendpunkte ermöglichen eine direkte Verbindung zwischen Ihrem virtuellen Netzwerk und unterstützten Azure-Diensten, sodass Sie mithilfe des privaten Adressraums Ihres virtuellen Netzwerks auf die Azure-Dienste zugreifen können. Datenverkehr, der über Dienstendpunkte für Azure-Ressourcen bestimmt ist, verbleibt immer im Microsoft Azure-Backbonenetzwerk. In diesem Artikel werden folgende Vorgehensweisen behandelt:

* Erstellen eines virtuellen Netzwerks mit einem Subnetz
* Hinzufügen eines Subnetzes und Aktivieren eines Dienstendpunkts
* Erstellen einer Azure-Ressource und Zulassen des Netzwerkzugriffs darauf ausschließlich aus einem Subnetz
* Bereitstellen eines virtuellen Computers für jedes Subnetz
* Bestätigen des Zugriffs auf eine Ressource aus einem Subnetz
* Bestätigen, dass der Zugriff auf eine Ressource aus einem Subnetz und dem Internet verweigert wird

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 1.0.0 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Vor der Erstellung eines virtuellen Netzwerks müssen Sie eine Ressourcengruppe für das virtuelle Netzwerk und alle anderen in diesem Artikel erstellten Ressourcen erstellen. Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe. Im folgenden Beispiel wird die Ressourcengruppe *myResourceGroup* erstellt: 

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Erstellen Sie mit [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) ein virtuelles Netzwerk. Im folgenden Beispiel wird ein virtuelles Netzwerk namens *myVirtualNetwork* mit dem Adresspräfix *10.0.0.0/16* erstellt.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Erstellen Sie mit [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) eine Subnetzkonfiguration. Im folgenden Beispiel wird eine Subnetzkonfiguration für das Subnetz *Public* erstellt:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Erstellen Sie das Subnetz im virtuellen Netzwerk, indem Sie die Subnetzkonfiguration mit [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork) in das virtuelle Netzwerk schreiben:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="enable-a-service-endpoint"></a>Aktivieren eines Dienstendpunkts

Sie können Dienstendpunkte nur für Dienste aktivieren, die Dienstendpunkte unterstützen. Dienstendpunktfähige Dienste, die an einem Azure-Standort verfügbar sind, können Sie mit [Get-AzVirtualNetworkAvailableEndpointService](/powershell/module/az.network/get-azvirtualnetworkavailableendpointservice) anzeigen. Im folgenden Beispiel wird eine Liste der dienstendpunktfähigen Dienste zurückgegeben, die in der Region *eastus* verfügbar sind. Die Liste der zurückgegebenen Dienste wird im Lauf der Zeit länger, da immer mehr Azure-Dienste für Dienstendpunkte aktiviert werden.

```azurepowershell-interactive
Get-AzVirtualNetworkAvailableEndpointService -Location eastus | Select Name
```

Erstellen Sie ein weiteres Subnetz im virtuellen Netzwerk. In diesem Beispiel wird das Subnetz *Private* mit einem Dienstendpunkt für *Microsoft.Storage* erstellt: 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-a-subnet"></a>Einschränken des Netzwerkzugriffs für ein Subnetz

Erstellen Sie mit [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) Netzwerksicherheitsgruppen-Sicherheitsregeln. Die folgende Regel erlaubt den ausgehenden Zugriff auf die öffentlichen IP-Adressen, die dem Azure Storage-Dienst zugewiesen sind: 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 `
  -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

Die folgende Regel verweigert den Zugriff auf alle öffentlichen IP-Adressen. Die vorherige Regel überschreibt diese Regel. Dies ist auf die höhere Priorität zurückzuführen, die den Zugriff auf die öffentlichen IP-Adressen von Azure Storage zulässt.

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name Deny-Internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 `
  -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

Die folgende Regel erlaubt eingehenden RDP-Datenverkehr (Remote Desktop Protocol) in das Subnetz von überall aus. Remotedesktopverbindungen sind im Subnetz zulässig, sodass Sie den Netzwerkzugriff auf eine Ressource in einem späteren Schritt bestätigen können.

```azurepowershell-interactive
$rule3 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-RDP-All `
  -Access Allow `
  -DestinationAddressPrefix VirtualNetwork `
  -DestinationPortRange 3389 `
  -Direction Inbound `
  -Priority 120 `
  -Protocol * `
  -SourceAddressPrefix * `
  -SourcePortRange *
```

Erstellen Sie mit [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) eine Netzwerksicherheitsgruppe. Im folgenden Beispiel wird eine Netzwerksicherheitsgruppe namens *myNsgPrivate* erstellt.

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

Ordnen Sie die Netzwerksicherheitsgruppe mit [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) dem Subnetz *Private* hinzu, und schreiben Sie dann die Subnetzkonfiguration in das virtuelle Netzwerk. Im folgenden Beispiel wird die Netzwerksicherheitsgruppe *myNsgPrivate* dem Subnetz *Private* hinzugefügt:

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-a-resource"></a>Einschränken des Netzwerkzugriffs auf eine Ressource

Die Schritte, die erforderlich sind, um den Netzwerkzugriff auf Ressourcen einzuschränken, die durch Azure-Dienste erstellt und für Dienstendpunkte aktiviert wurden, sind je nach Dienst unterschiedlich. Informationen zu den Schritten für einzelne Dienste finden Sie in der Dokumentation des jeweiligen Diensts. Der Rest dieses Artikels enthält als Beispiel Schritte zum Einschränken des Netzwerkzugriffs für ein Azure Storage-Konto.

### <a name="create-a-storage-account"></a>Speicherkonto erstellen

Erstellen Sie mit [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) ein Azure Storage-Konto. Ersetzen Sie `<replace-with-your-unique-storage-account-name>` durch einen Namen, der an allen Azure-Standorten eindeutig, zwischen 3 und 24 Zeichen lang ist und nur aus Ziffern und Kleinbuchstaben besteht.

```azurepowershell-interactive
$storageAcctName = '<replace-with-your-unique-storage-account-name>'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Rufen Sie nach dem Erstellen des Speicherkontos mit [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) den Schlüssel für das Speicherkonto in eine Variable ab:

```azurepowershell-interactive
$storageAcctKey = (Get-AzStorageAccountKey `
  -ResourceGroupName myResourceGroup `
  -AccountName $storageAcctName).Value[0]
```

Mit dem Schlüssel wird in einem späteren Schritt eine Dateifreigabe erstellt. Geben Sie `$storageAcctKey` ein, und notieren Sie den Wert. Diesen müssen Sie in einem späteren Schritt auch manuell eingeben, wenn Sie die Dateifreigabe einem Laufwerk in einem virtuellen Computer zuordnen.

### <a name="create-a-file-share-in-the-storage-account"></a>Erstellen einer Dateifreigabe im Speicherkonto

Erstellen Sie mit [New-AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext) einen Kontext für das Speicherkonto und den Speicherschlüssel. Der Kontext kapselt den Speicherkontonamen und den Kontoschlüssel:

```azurepowershell-interactive
$storageContext = New-AzStorageContext $storageAcctName $storageAcctKey
```

Erstellen Sie mit [New-AzStorageShare](/powershell/module/az.storage/new-azstorageshare) eine Dateifreigabe:

$share = New-AzStorageShare my-file-share -Context $storageContext

### <a name="deny-all-network-access-to-a-storage-account"></a>Verweigern des gesamten Netzwerkzugriffs auf ein Speicherkonto

Standardmäßig akzeptieren Speicherkonten Netzwerkverbindungen von Clients in allen Netzwerken. Um den Zugriff auf ausgewählte Netzwerke einzuschränken, ändern Sie die Standardaktion mit [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset) in *Deny* (Verweigern). Nachdem der Netzwerkzugriff verweigert wurde, kann auf das Speicherkonto aus keinem Netzwerk mehr zugegriffen werden.

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -DefaultAction Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Aktivieren des Netzwerkzugriffs aus einem Subnetz

Rufen Sie mit [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) das erstellte virtuelle Netzwerk und dann mit [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) das Subnetzobjekt „Private“ in eine Variable ab:

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Name "myVirtualNetwork" `
  | Get-AzVirtualNetworkSubnetConfig `
  -Name "Private"
```

Erlauben Sie den Netzwerkzugriff auf das Speicherkonto aus dem Subnetz *Private* mit [Add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig).

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Zum Testen des Netzwerkzugriffs auf ein Speicherkonto stellen Sie einen virtuellen Computer für jedes Subnetz bereit.

### <a name="create-the-first-virtual-machine"></a>Erstellen des ersten virtuellen Computers

Erstellen Sie mit *New-AzVM* einen virtuellen Computer im Subnetz [Öffentlich](/powershell/module/az.compute/new-azvm). Wenn Sie den folgenden Befehl ausführen, werden Sie aufgefordert, Anmeldeinformationen einzugeben. Die eingegebenen Werte werden als Benutzername und Kennwort für den virtuellen Computer konfiguriert. Mit der Option `-AsJob` wird der virtuelle Computer im Hintergrund erstellt, sodass Sie mit dem nächsten Schritt fortfahren können.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -Name "myVmPublic" `
    -AsJob
```

Die zurückgegebene Ausgabe ähnelt der folgenden Beispielausgabe:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM     
```

### <a name="create-the-second-virtual-machine"></a>Erstellen des zweiten virtuellen Computers

Erstellen Sie einen virtuellen Computer im Subnetz *Private*:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -Name "myVmPrivate"
```

Das Erstellen des virtuellen Computers für Azure dauert einige Minuten. Fahren Sie erst mit dem nächsten Schritt fort, nachdem der virtuelle Computer in Azure erstellt und eine Ausgabe in PowerShell zurückgegeben wurde.

## <a name="confirm-access-to-storage-account"></a>Bestätigen des Zugriffs auf das Speicherkonto

Geben Sie mit [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) die öffentliche IP-Adresse eines virtuellen Computers zurück. Das folgende Beispiel gibt die öffentliche IP-Adresse der *myVmPrivate*-VM zurück:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Ersetzen Sie im folgenden Befehl `<publicIpAddress>` durch die vom vorherigen Befehl zurückgegebene öffentliche IP-Adresse, und geben Sie dann den folgenden Befehl ein:

```powershell
mstsc /v:<publicIpAddress>
```

Eine RDP-Datei (Remotedesktopprotokoll) wird erstellt und auf Ihren Computer heruntergeladen. Öffnen Sie die heruntergeladene RDP-Datei. Wenn Sie dazu aufgefordert werden, wählen Sie **Verbinden** aus. Geben Sie den Benutzernamen und das Kennwort ein, die Sie beim Erstellen des virtuellen Computers festgelegt haben. Unter Umständen müssen Sie auf **Weitere Optionen** und anschließend auf **Anderes Konto verwenden** klicken, um die Anmeldeinformationen anzugeben, die Sie beim Erstellen des virtuellen Computers eingegeben haben. Klicken Sie auf **OK**. Während des Anmeldevorgangs wird unter Umständen eine Zertifikatwarnung angezeigt. Wenn eine Warnung angezeigt wird, klicken Sie auf **Ja** bzw. **Weiter**, um mit dem Herstellen der Verbindung fortzufahren.

Ordnen Sie auf dem virtuellen Computer *myVmPrivate* mithilfe von PowerShell die Azure-Dateifreigabe dem Laufwerk Z zu. Ersetzen Sie vor dem Ausführen der nachfolgenden Befehle die Werte `<storage-account-key>` und `<storage-account-name>` durch Werte, die Sie unter [Erstellen eines Speicherkontos](#create-a-storage-account) angegeben oder abgerufen haben.

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

PowerShell gibt eine Ausgabe ähnlich der folgenden Beispielausgabe zurück:

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
```

Die Azure-Dateifreigabe wurde dem Laufwerk Z erfolgreich zugeordnet.

Bestätigen Sie, dass der virtuelle Computer über keine ausgehende Verbindung mit einer beliebigen anderen öffentlichen IP-Adresse verfügt:

```powershell
ping bing.com
```

Sie erhalten keine Antworten, da die dem Subnetz *Private* zugeordnete Netzwerksicherheitsgruppe keinen ausgehenden Zugriff auf andere öffentliche IP-Adressen als auf die dem Azure Storage-Dienst zugewiesenen Adressen erlaubt.

Schließen Sie die Remotedesktopsitzung mit der VM *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Bestätigen, dass der Zugriff auf das Speicherkonto verweigert wird

Rufen Sie die öffentliche IP-Adresse des virtuellen Computers *myVmPublic* ab:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPublic `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Ersetzen Sie im folgenden Befehl `<publicIpAddress>` durch die vom vorherigen Befehl zurückgegebene öffentliche IP-Adresse, und geben Sie dann den folgenden Befehl ein: 

```powershell
mstsc /v:<publicIpAddress>
```

Versuchen Sie, auf dem virtuellen Computer *myVmPublic* die Azure-Dateifreigabe dem Laufwerk Z zuzuordnen. Ersetzen Sie vor dem Ausführen der nachfolgenden Befehle die Werte `<storage-account-key>` und `<storage-account-name>` durch Werte, die Sie unter [Erstellen eines Speicherkontos](#create-a-storage-account) angegeben oder abgerufen haben.

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

Der Zugriff auf die Freigabe wird verweigert, und die Fehlermeldung `New-PSDrive : Access is denied` wird angezeigt. Der Zugriff wird verweigert, da der virtuelle Computer *myVmPublic* im Subnetz *Public* bereitgestellt wird. Für das Subnetz *Public* ist kein Dienstendpunkt für Azure Storage aktiviert, und das Speicherkonto erlaubt nur den Netzwerkzugriff aus dem Subnetz *Private*, nicht aus dem Subnetz *Public*.

Schließen Sie die Remotedesktopsitzung für den virtuellen Computer *myVmPublic*.

Versuchen Sie von Ihrem Computer aus, die Dateifreigaben im Speicherkonto mit dem folgenden Befehl anzuzeigen:

```powershell-interactive
Get-AzStorageFile `
  -ShareName my-file-share `
  -Context $storageContext
```

Der Zugriff wird verweigert, und Sie erhalten den Fehler: *Get-AzStorageFile: Der Remoteserver hat einen Fehler zurückgegeben: (403) Verboten. HTTP-Statuscode: 403 – HTTP-Fehlermeldung: Diese Anforderung ist nicht berechtigt, diesen Vorgang auszuführen*, da sich Ihr Computer nicht im Subnetz *Private* des virtuellen Netzwerks *MyVirtualNetwork* befindet.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcengruppe und alle darin enthaltenen Ressourcen nicht mehr benötigen, können Sie sie mit dem Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) entfernen:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie einen Dienstendpunkt für ein Subnetz eines virtuellen Netzwerks aktiviert. Sie haben erfahren, dass Dienstendpunkte für Ressourcen aktiviert werden können, die mit mehreren Azure-Diensten bereitgestellt werden. Sie haben ein Azure Storage-Konto erstellt und den Netzwerkzugriff auf das Speicherkonto ausschließlich auf Ressourcen im Subnetz eines virtuellen Netzwerks eingeschränkt. Weitere Informationen zu Dienstendpunkten finden Sie unter [Dienstendpunkte im virtuellen Netzwerk](virtual-network-service-endpoints-overview.md) und [Hinzufügen, Ändern oder Löschen von Subnetzen virtueller Netzwerke](virtual-network-manage-subnet.md).

Wenn Sie mehrere virtuelle Netzwerke in Ihrem Konto verwenden, können Sie zwei virtuelle Netzwerke miteinander verbinden, damit die Ressourcen in jedem virtuellen Netzwerk miteinander kommunizieren können. Informationen zur Vorgehensweise finden Sie unter [Herstellen von Verbindungen zwischen virtuellen Netzwerken](tutorial-connect-virtual-networks-powershell.md).
