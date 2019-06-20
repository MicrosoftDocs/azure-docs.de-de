---
title: Verschlüsseln von Datenträgern auf einem virtuellen Windows-Computer in Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie virtuelle Datenträger auf einem virtuellen Windows-Computer mithilfe von Azure PowerShell verschlüsseln, um die Sicherheit zu erhöhen.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: 4ef485bb91fe52e138b805f347e729fc4097fc7c
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431099"
---
# <a name="encrypt-virtual-disks-on-a-windows-vm"></a>Verschlüsseln virtueller Datenträger auf einer Windows-VM
Zum Verbessern der Sicherheit und Compliance von virtuellen Computern können virtuelle Datenträger in Azure verschlüsselt werden. Die Verschlüsselung der Datenträger basiert auf kryptografischen Schlüsseln, die in einer Azure Key Vault-Instanz gesichert werden. Diese kryptografischen Schlüssel werden von Ihnen kontrolliert, und Sie können deren Verwendung überwachen. Dieser Artikel beschreibt, wie Sie virtuelle Datenträger auf einer Windows-VM mithilfe von Azure PowerShell verschlüsseln. Sie können auch [einen virtuellen Linux-Computer mithilfe der Azure CLI verschlüsseln](../linux/encrypt-disks.md).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="overview-of-disk-encryption"></a>Übersicht über die Datenträgerverschlüsselung
Virtuelle Datenträger auf virtuellen Windows-Computern werden im Ruhezustand mit BitLocker verschlüsselt. Für die Verschlüsselung virtueller Datenträger in Azure fallen keine Gebühren an. Kryptografische Schlüssel werden in einer Azure Key Vault-Instanz mit Softwareschutz gespeichert. Alternativ können Sie Schlüssel auch in Hardwaresicherheitsmodulen (HSMs) mit FIPS 140-2 Level 2-Zertifizierung importieren oder generieren. Kryptografische Schlüssel dienen dem Verschlüsseln und Entschlüsseln virtueller Datenträger, die Ihrer VM angefügt sind. Sie behalten die Kontrolle über diese kryptografischen Schlüssel und können deren Verwendung überwachen. 

Gehen Sie zum Verschlüsseln eines virtuellen Computers wie folgt vor:

1. Erstellen Sie in einer Azure Key Vault-Instanz einen kryptografischen Schlüssel.
1. Konfigurieren Sie den kryptografischen Schlüssel so, dass er zum Verschlüsseln von Datenträgern verwendet werden kann.
1. Aktivieren Sie die Datenträgerverschlüsselung für Ihre virtuellen Datenträger.
1. Die erforderlichen kryptografischen Schlüssel werden von Azure Key Vault angefordert.
1. Die virtuellen Datenträger werden mithilfe des angegebenen kryptografischen Schlüssels verschlüsselt.


## <a name="requirements-and-limitations"></a>Voraussetzungen und Einschränkungen

Unterstützte Szenarien und Voraussetzungen für die Datenträgerverschlüsselung:

* Aktivieren der Verschlüsselung auf neuen Windows-VMs über Azure Marketplace-Images oder benutzerdefinierte VHD-Images
* Aktivieren der Verschlüsselung auf vorhandenen virtuellen Windows-Computern in Azure
* Aktivieren der Verschlüsselung auf Windows-VMs, die mit Speicherplätzen konfiguriert wurden
* Deaktivieren der Verschlüsselung auf Betriebssystem- und Datenlaufwerken für virtuelle Windows-Computer
* Virtuelle Computer im Standard-Tarif, z.B. virtuelle Computer der Reihen A, D, DS, G und GS

    > [!NOTE]
    > Alle Ressourcen (z.B. Key Vault, Speicherkonto und VM) müssen sich in der gleichen Azure-Region und im gleichen Abonnement befinden.

Die Datenträgerverschlüsselung wird derzeit in folgenden Szenarien nicht unterstützt:

* Bei virtuellen Computern des Basic-Tarifs
* Auf VMs, die mit dem klassischen Bereitstellungsmodell erstellt wurden
* Bei Aktualisierung der kryptografischen Schlüssel auf einem bereits verschlüsselten virtuellen Computer
* Integration in den lokalen Schlüsselverwaltungsdienst


## <a name="create-an-azure-key-vault-and-keys"></a>Erstellen einer Azure Key Vault-Instanz und von Schlüsseln
Überprüfen Sie vor Beginn, ob die neueste Version des Azure PowerShell-Moduls installiert ist. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview). Ersetzen Sie in den folgenden Befehlsbeispielen alle Beispielparameter durch Ihre eigenen Werte für Namen, den Speicherort und den Schlüssel, z.B. *myResourceGroup*, *myKeyVault*, *myVM* usw.

Erstellen Sie zum Speichern Ihrer kryptografischen Schlüssel zunächst eine Azure Key Vault-Instanz. In Azure Key Vault können Schlüssel, Geheimnisse und Kennwörter gespeichert werden, um eine sichere Implementierung in Ihre Anwendungen und Dienste zu ermöglichen. Beim Verschlüsseln virtueller Datenträger erstellen Sie einen Schlüsseltresor zum Speichern eines kryptografischen Schlüssels, der zum Verschlüsseln oder Entschlüsseln der virtuellen Datenträger verwendet wird. 

Aktivieren Sie in Ihrem Azure-Abonnement mit [Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider) den Azure Key Vault-Anbieter, und erstellen Sie mit [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *USA, Osten*:

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzResourceGroup -Location $location -Name $rgName
```

Die Azure Key Vault-Instanz mit den kryptografischen Schlüsseln und den dazugehörigen Computeressourcen wie Speicher sowie die VM selbst müssen sich in der gleichen Region befinden Erstellen Sie mit [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) eine Azure Key Vault-Instanz, und aktivieren Sie die Key Vault-Instanz für die Verwendung mit Datenträgerverschlüsselung. Geben Sie einen eindeutigen Schlüsseltresornamen für *keyVaultName* an, wie nachfolgend gezeigt:

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Kryptografische Schlüssel können mit Softwareschutz oder mit HSM-Schutz gespeichert werden.  Eine Key Vault-Standardinstanz speichert nur softwaregeschützte Schlüssel. Das Verwenden eines Hardwaresicherheitsmoduls erfordert eine Key Vault-Premiuminstanz. Um diese zu erstellen, fügen Sie im vorherigen Schritt den Parameter *-SKU „Premium“* hinzu. Im folgenden Beispiel werden softwaregeschützte Schlüssel verwendet, da wir eine Key Vault-Standardinstanz erstellt haben. 

Bei beiden Schutzmodellen muss der Azure-Plattform Zugriff gewährt werden, um beim Start des virtuellen Computers die kryptografischen Schlüssel anfordern und die virtuellen Datenträger entschlüsseln zu können. Erstellen Sie mithilfe von [Add-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey) einen kryptografischen Schlüssel in Ihrem Schlüsseltresor. Im folgenden Beispiel wird ein Schlüssel mit dem Namen *myKey* erstellt:

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers
Um den Verschlüsselungsvorgang zu testen, erstellen Sie einen virtuellen Computer mit [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Im folgenden Beispiel wird unter Verwendung eines *Windows Server 2016 Datacenter*-Images der virtuelle Computer *myVM* erstellt. Geben Sie bei entsprechender Aufforderung den Benutzernamen und das Kennwort für den virtuellen Computer ein:

```azurepowershell-interactive
$cred = Get-Credential

New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## <a name="encrypt-a-virtual-machine"></a>Verschlüsseln einer VM
Verschlüsseln Sie Ihre VM mit [Set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) unter Verwendung des Azure Key Vault-Schlüssels. Im folgenden Beispiel werden alle Schlüsselinformationen abgerufen und anschließend der virtuelle Computer *myVM* verschlüsselt:

```azurepowershell-interactive
$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

Bestätigen Sie die Aufforderung zum Fortsetzen der Verschlüsselung des virtuellen Computers. Der virtuelle Computer wird während des Vorgangs neu gestartet. Nach Abschluss des Verschlüsselungsvorgangs und dem Neustart des virtuellen Computers können Sie den Verschlüsselungsstatus mit [Get-AzVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus) überprüfen:

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```azurepowershell-interactive
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum Verwalten von Azure Key Vault-Instanzen finden Sie unter [Einrichten des Schlüsseltresors für virtuelle Computer](key-vault-setup.md).
* Weitere Informationen zur Datenträgerverschlüsselung (etwa zum Vorbereiten des Uploads eines verschlüsselten benutzerdefinierten virtuellen Computers in Azure) finden Sie unter [Azure-Datenträgerverschlüsselung für virtuelle Windows- und Linux-IaaS-Computer](../../security/azure-security-disk-encryption.md).
