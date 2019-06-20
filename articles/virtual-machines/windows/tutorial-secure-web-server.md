---
title: Tutorial – Sichern eines Windows-Webservers mit SSL-Zertifikaten in Azure | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie Azure PowerShell zum Sichern eines virtuellen Windows-Computers verwenden, auf dem der IIS-Webserver mit im Azure Key Vault gespeicherten SSL-Zertifikaten ausgeführt wird.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 2b92700caab27b527ae58cc0c7e8deca89c4d43f
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66727913"
---
# <a name="tutorial-secure-a-web-server-on-a-windows-virtual-machine-in-azure-with-ssl-certificates-stored-in-key-vault"></a>Tutorial: Sichern eines Webservers auf einem virtuellen Windows-Computer in Azure mit in Key Vault gespeicherten SSL-Zertifikaten

Zum Sichern von Webservern kann ein SSL-Zertifikat (Secure Sockets Layer) zum Verschlüsseln des Webdatenverkehrs verwendet werden. Diese SSL-Zertifikate können in Azure Key Vault gespeichert werden. Sie ermöglichen sichere Bereitstellungen von Zertifikaten auf virtuellen Windows-Computern in Azure. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer Azure Key Vault-Instanz
> * Generieren oder Hochladen eines Zertifikats in Key Vault
> * Erstellen eines virtuellen Computers und Installieren des IIS-Webservers
> * Einfügen des Zertifikats auf dem virtuellen Computer und Konfigurieren von IIS mit einer SSL-Bindung

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="launch-azure-cloud-shell"></a>Starten von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. 

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock einfach die Option **Ausprobieren**. Sie können Cloud Shell auch auf einer separaten Browserregisterkarte starten, indem Sie zu [https://shell.azure.com/powershell](https://shell.azure.com/powershell) navigieren. Wählen Sie **Kopieren**, um die Blöcke mit dem Code zu kopieren. Fügen Sie ihn anschließend in Cloud Shell ein, und drücken Sie die EINGABETASTE, um ihn auszuführen.


## <a name="overview"></a>Übersicht
Azure Key Vault schützt Kryptografieschlüssel und geheime Schlüssel, solche Zertifikate oder Kennwörter. Key Vault optimiert die Zertifikatverwaltung und ermöglicht Ihnen, die Kontrolle über Schlüssel zu behalten, die auf diese Zertifikate zugreifen. Sie können ein selbstsigniertes Zertifikat in Key Vault erstellen oder ein vorhandenes vertrauenswürdiges Zertifikat verwenden, das Sie bereits besitzen.

Anstatt ein benutzerdefiniertes VM-Image zu verwenden, in dem die Zertifikate integriert sind, fügen Sie Zertifikate in einen ausgeführten virtuellen Computer ein. Dadurch wird sichergestellt, dass auf einem Webserver während der Bereitstellung die aktuellen Zertifikate installiert sind. Wenn Sie ein Zertifikat erneuern oder ersetzen, müssen Sie nicht auch noch ein neues benutzerdefiniertes VM-Image erstellen. Die neuesten Zertifikate werden beim Erstellen weiterer virtueller Computer automatisch eingefügt. Während des gesamten Prozesses verlässt das Zertifikat nie die Azure-Plattform, noch wird es in einem Skript, Befehlszeilenverlauf oder einer Vorlage verfügbar gemacht.


## <a name="create-an-azure-key-vault"></a>Erstellen einer Azure Key Vault-Instanz
Bevor Sie eine Key Vault-Instanz und Zertifikate erstellen, müssen Sie mit [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe erstellen. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroupSecureWeb* am Standort *USA, Osten* erstellt:

```azurepowershell-interactive
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

Erstellen Sie als Nächstes mit [New-AzureRmKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) eine Key Vault-Instanz. Jede Key Vault-Instanz benötigt einen eindeutigen Namen, der nur aus Kleinbuchstaben besteht. Ersetzen Sie `mykeyvault` im folgenden Beispiel durch Ihren eigenen eindeutigen Key Vault-Namen:

```azurepowershell-interactive
$keyvaultName="mykeyvault"
New-AzKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Generieren eines Zertifikats und Speichern in Key Vault
Für die Produktion sollten Sie ein gültiges, von einem vertrauenswürdigen Anbieter signiertes Zertifikat mit [Import-AzureKeyVaultCertificate](https://docs.microsoft.com/powershell/module/az.keyvault/import-azkeyvaultcertificate) importieren. Für dieses Tutorial zeigt das folgende Beispiel, wie Sie ein selbstsigniertes Zertifikat mit [Add-AzureKeyVaultCertificate](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultcertificate) generieren können, das die Standardzertifikatrichtlinie von [New-AzureKeyVaultCertificatePolicy](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvaultcertificatepolicy) verwendet: 

```azurepowershell-interactive
$policy = New-AzureKeyVaultCertificatePolicy `
    -SubjectName "CN=www.contoso.com" `
    -SecretContentType "application/x-pkcs12" `
    -IssuerName Self `
    -ValidityInMonths 12

Add-AzureKeyVaultCertificate `
    -VaultName $keyvaultName `
    -Name "mycert" `
    -CertificatePolicy $policy 
```


## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers
Legen Sie mit [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) den Benutzernamen und das Kennwort des Administrators des virtuellen Computers fest:

```azurepowershell-interactive
$cred = Get-Credential
```

Nun können Sie mit [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) den virtuellen Computer erstellen. Im folgenden Beispiel wird eine VM mit dem Namen *myVM* für den Standort *EastUS* erstellt. Falls sie nicht bereits vorhanden sind, werden die unterstützenden Netzwerkressourcen erstellt. Um sicheren Webdatenverkehr zuzulassen, öffnet das Cmdlet auch Port *443*.

```azurepowershell-interactive
# Create a VM
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred `
    -OpenPorts 443

# Use the Custom Script Extension to install IIS
Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server -IncludeManagementTools"}'
```

Die Erstellung des virtuellen Computers dauert einige Minuten. Im letzten Schritt wird die benutzerdefinierte Skripterweiterung von Azure zum Installieren des IIS-Webservers mit [Set-AzVmExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) verwendet.


## <a name="add-a-certificate-to-vm-from-key-vault"></a>Hinzufügen eines Zertifikats zum virtuellen Computer aus Key Vault
Um das Zertifikat aus Key Vault zu einem virtuellen Computer hinzuzufügen, rufen Sie die ID des Zertifikats mit [Get-AzureKeyVaultSecret](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvaultsecret) ab. Fügen Sie das Zertifikat mit [Add-AzVMSecret](https://docs.microsoft.com/powershell/module/az.compute/add-azvmsecret) dem virtuellen Computer hinzu:

```azurepowershell-interactive
$certURL=(Get-AzureKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>Konfigurieren von IIS zur Verwendung des Zertifikats
Verwenden Sie die benutzerdefinierte Skripterweiterung erneut mit [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension), um die IIS-Konfiguration zu aktualisieren. Bei dieser Aktualisierung wird das aus Key Vault eingefügte Zertifikat auf IIS angewendet und die Webbindung konfiguriert:

```azurepowershell-interactive
$PublicSettings = '{
    "fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/secure-iis.ps1"],
    "commandToExecute":"powershell -ExecutionPolicy Unrestricted -File secure-iis.ps1"
}'

Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString $publicSettings
```


### <a name="test-the-secure-web-app"></a>Testen der sicheren Web-App
Rufen Sie mit [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) die öffentliche IP-Adresse Ihres virtuellen Computers ab. Im folgenden Beispiel wird die IP-Adresse für `myPublicIP` abgerufen, die wir zuvor erstellt haben:

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIPAddress" | select "IpAddress"
```

Jetzt können Sie einen Webbrowser öffnen und `https://<myPublicIP>` in die Adressleiste eingeben. Wenn Sie ein selbstsigniertes Zertifikat verwendet haben und die Sicherheitswarnung akzeptieren möchten, klicken Sie auf **Details** und anschließend auf **Webseite trotzdem laden**:

![Akzeptieren der Webbrowser-Sicherheitswarnung](./media/tutorial-secure-web-server/browser-warning.png)

Die gesicherte IIS-Website wird dann wie im folgenden Beispiel angezeigt:

![Anzeigen der ausgeführten sicheren IIS-Website](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie einen IIS-Webserver mit einem in Azure Key Vault gespeicherten SSL-Zertifikat gesichert. Es wurde Folgendes vermittelt:

> [!div class="checklist"]
> * Erstellen einer Azure Key Vault-Instanz
> * Generieren oder Hochladen eines Zertifikats in Key Vault
> * Erstellen eines virtuellen Computers und Installieren des IIS-Webservers
> * Einfügen des Zertifikats auf dem virtuellen Computer und Konfigurieren von IIS mit einer SSL-Bindung

Folgen Sie diesem Link, um sich vordefinierte Skriptbeispiele für virtuelle Computer anzusehen.

> [!div class="nextstepaction"]
> [Virtueller Azure-Computer – PowerShell-Beispiele](./powershell-samples.md)
