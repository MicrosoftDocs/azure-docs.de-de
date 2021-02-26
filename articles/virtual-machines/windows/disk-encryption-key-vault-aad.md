---
title: Erstellen und Konfigurieren eines Schlüsseltresors für Azure Disk Encryption mit Azure AD (vorheriges Release)
description: In diesem Artikel wird beschrieben, wie Sie einen Schlüsseltresor für Azure Disk Encryption mit Azure AD erstellen und konfigurieren.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: how-to
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: fd239b52e647d7a0dfa3b60ee1dc31ea7005311b
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805954"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release"></a>Erstellen und Konfigurieren eines Schlüsseltresors für Azure Disk Encryption mit Azure AD (vorheriges Release)

**Beim neuen Release von Azure Disk Encryption muss kein Azure AD-Anwendungsparameter mehr angegeben werden, um die VM-Datenträgerverschlüsselung zu aktivieren. Sie müssen beim neuen Release während des Schritts zum Aktivieren der Verschlüsselung keine Azure AD-Anmeldeinformationen mehr angeben. Alle neuen virtuellen Computer müssen mit dem neuen Release und ohne die Azure AD-Anwendungsparameter verschlüsselt werden. Eine Anleitung zum Aktivieren der VM-Datenträgerverschlüsselung mit dem neuen Release finden Sie unter [Azure Disk Encryption](disk-encryption-overview.md). Virtuelle Computer, die bereits mit Azure AD-Anwendungsparametern verschlüsselt wurden, werden weiterhin unterstützt und sollten weiterhin mit der AAD-Syntax gepflegt werden.**

Azure Disk Encryption verwendet Azure Key Vault zum Steuern und Verwalten von Verschlüsselungsschlüsseln und Geheimnissen für die Datenträgerverschlüsselung.  Weitere Informationen zu Schlüsseltresoren finden Sie unter [Erste Schritte mit Azure Key Vault](../../key-vault/general/overview.md) und [Schützen eines Schlüsseltresors](../../key-vault/general/secure-your-key-vault.md). 

Das Erstellen und Konfigurieren eines Schlüsseltresors für Azure Disk Encryption mit Azure AD (vorheriges Release) umfasst drei Schritte:

1. Erstellen eines Schlüsseltresors 
2. Richten Sie eine Azure AD-Anwendung und einen Dienstprinzipal ein.
3. Legen Sie die Zugriffsrichtlinie für den Schlüsseltresor für die Azure AD-App fest.
4. Legen Sie die erweiterten Zugriffsrichtlinien für den Schlüsseltresor fest.
 
Sie können auch einen Schlüssel für die Schlüsselverschlüsselung (Key Encryption Key, KEK) generieren oder importieren.

Weitere Informationen zum [Installieren von Tools und Herstellen einer Verbindung mit Azure](disk-encryption-key-vault.md#install-tools-and-connect-to-azure) finden Sie im Hauptartikel [Erstellen und Konfigurieren eines Schlüsseltresors für Azure Disk Encryption](disk-encryption-key-vault.md).

> [!Note]
> Die Schritte in diesem Artikel werden im [CLI-Skript für die Voraussetzungen für Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started) und im [PowerShell-Skript für die Voraussetzungen für Azure Disk Encryption](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts) automatisiert.


## <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors 
Azure Disk Encryption ist in [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) integriert, damit Sie die Verschlüsselungsschlüssel und Geheimnisse für die Datenträgerverschlüsselung in Ihrem Key Vault-Abonnement steuern und verwalten können. Sie können für Azure Disk Encryption einen neuen Schlüsseltresor erstellen oder einen bereits vorhandenen verwenden. Weitere Informationen zu Schlüsseltresoren finden Sie unter [Erste Schritte mit Azure Key Vault](../../key-vault/general/overview.md) und [Schützen eines Schlüsseltresors](../../key-vault/general/secure-your-key-vault.md). Sie können eine Resource Manager-Vorlage, Azure PowerShell oder die Azure CLI verwenden, um einen Schlüsseltresor zu erstellen. 


>[!WARNING]
>Um sicherzustellen, dass die Verschlüsselungsgeheimnisse die Regionsgrenzen nicht verlassen, müssen den Schlüsseltresor und die VMs für Azure Disk Encryption sich in derselben Region angeordnet sein. Erstellen und verwenden Sie einen Schlüsseltresor, der sich in derselben Region wie die zu verschlüsselnde VM befindet. 


### <a name="create-a-key-vault-with-powershell"></a>Erstellen eines Schlüsseltresors mit PowerShell

Sie können mit Azure PowerShell mit dem Cmdlet [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) einen Schlüsseltresor erstellen. Weitere Cmdlets für Key Vault finden Sie unter [Az.KeyVault](/powershell/module/az.keyvault/). 

1. Erstellen Sie ggf. mit [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) eine neue Ressourcengruppe.  Verwenden Sie [Get-AzLocation](/powershell/module/az.resources/get-azlocation), um Standorte von Rechenzentren aufzulisten. 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

1. Erstellen Sie mit [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) einen neuen Schlüsseltresor.
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Notieren Sie **Tresorname**, **Ressourcengruppenname**, **Ressourcen-ID**, **Vault-URI** und **Objekt-ID**, die zur späteren Verwendung beim Verschlüsseln der Datenträger zurückgegeben werden. 


### <a name="create-a-key-vault-with-azure-cli"></a>Erstellen eines Schlüsseltresors mit der Azure-Befehlszeilenschnittstelle
Sie können Ihren Schlüsseltresor mit der Azure CLI mit den [az keyvault](/cli/azure/keyvault#commands)-Befehlen verwalten. Verwenden Sie [az keyvault create](/cli/azure/keyvault#az-keyvault-create), um einen Schlüsseltresor zu erstellen.

1. Erstellen Sie ggf. mit [az group create](/cli/azure/group#az-group-create) eine neue Ressourcengruppe. Verwenden Sie [az account list-locations](/cli/azure/account#az-account-list), um Orte aufzulisten. 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Erstellen Sie mit [az keyvault create](/cli/azure/keyvault#az-keyvault-create) einen neuen Schlüsseltresor.
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Notieren Sie **Tresorname** (Name), **Ressourcengruppenname**, **Ressourcen-ID** (ID), **Vault-URI** und **Objekt-ID**, die zur späteren Verwendung zurückgegeben werden. 

### <a name="create-a-key-vault-with-a-resource-manager-template"></a>Erstellen eines Schlüsseltresors mit einer Resource Manager-Vorlage

Sie können mit der [Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create) einen Schlüsseltresor erstellen.

1. Klicken Sie in der Azure-Schnellstartvorlage auf **Deploy to Azure** (In Azure bereitstellen).
2. Wählen Sie Abonnement, Ressourcengruppe, Ressourcengruppenstandort, Schlüsseltresorname, Objekt-ID, rechtliche Bedingungen und Vereinbarung aus, und klicken Sie auf **Kaufen**. 


## <a name="set-up-an-azure-ad-app-and-service-principal"></a>Einrichten einer Azure AD-App und eines Dienstprinzipals 
Wenn Sie die Verschlüsselung auf einem ausgeführten virtuellen Computer in Azure aktivieren müssen, werden die Verschlüsselungsschlüssel von Azure Disk Encryption generiert und in Ihren Schlüsseltresor geschrieben. Das Verwalten von Verschlüsselungsschlüsseln im Schlüsseltresor erfordert eine Azure AD-Authentifizierung. Erstellen Sie dafür eine Azure AD-Anwendung. Zu Authentifizierungszwecken können Sie entweder die auf einem geheimen Clientschlüssel basierende Authentifizierung oder die [auf einem Clientzertifikat basierende Azure AD-Authentifizierung](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) verwenden.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-powershell"></a>Einrichten einer Azure AD-App und eines Dienstprinzipals mit Azure PowerShell 
Für die folgenden Befehle benötigen Sie das [Azure AD PowerShell-Modul](/powershell/azure/active-directory/install-adv2). 

1. Verwenden Sie das PowerShell-Cmdlet [New-AzADApplication](/powershell/module/az.resources/new-azadapplication), um eine Azure AD-Anwendung zu erstellen. Die Werte für „MyApplicationHomePage“ und „MyApplicationUri“ können beliebig sein.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. „$azureAdApplication.ApplicationId“ ist die Azure AD-Client-ID, und „$aadClientSecret“ ist der geheime Clientschlüssel. Sie benötigen diese Angaben später zum Aktivieren von Azure Disk Encryption. Sorgen Sie für eine sichere Aufbewahrung des Azure AD-Clientgeheimnisses. Wenn Sie `$azureAdApplication.ApplicationId` ausführen, wird Ihnen die „ApplicationID“ angezeigt.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-cli"></a>Einrichten einer Azure AD-App und eines Dienstprinzipals mit der Azure-Befehlszeilenschnittstelle

Sie können Ihre Dienstprinzipale mit der Azure CLI mit den [az ad sp](/cli/azure/ad/sp)-Befehlen verwalten. Weitere Informationen finden Sie unter [Erstellen eines Azure-Dienstprinzipals](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Erstellen Sie einen neuen Dienstprinzipal.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  Die zurückgegeben App-ID ist die Azure AD-Client-ID, die in anderen Befehlen verwendet wird. Sie ist außerdem der Dienstprinzipalname für „az keyvault set-policy“. Das Kennwort ist der geheime Clientschlüssel, mit dem Sie später Azure Disk Encryption aktivieren werden. Sorgen Sie für eine sichere Aufbewahrung des Azure AD-Clientgeheimnisses.
 
### <a name="set-up-an-azure-ad-app-and-service-principal-though-the-azure-portal"></a>Einrichten einer Azure AD-App und eines Dienstprinzipals über das Azure-Portal
Im Artikel [Erstellen einer Azure Active Directory-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff mithilfe des Portals](../../active-directory/develop/howto-create-service-principal-portal.md) finden Sie eine Anleitung, wie Sie eine Azure AD-Anwendung erstellen. Jeder unten aufgeführte Schritt führt Sie direkt zum entsprechenden Artikelabschnitt. 

1. [Überprüfen Sie die erforderlichen Berechtigungen.](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)
2. [Erstellen Sie eine Azure Active Directory-Anwendung.](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) 
     - Sie können einen beliebigen Namen und eine beliebige Anmelde-URL verwenden, wenn Sie die Anwendung erstellen.
3. [Rufen Sie die Anwendungs-ID und den Authentifizierungsschlüssel ab.](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) 
     - Der Authentifizierungsschlüssel ist der geheime Clientschlüssel und wird als „AadClientSecret“ für „Set-AzVMDiskEncryptionExtension“ verwendet. 
        - Der Authentifizierungsschlüssel wird von der Anwendung als Anmeldeinformation für die Anmeldung in Azure AD verwendet. Im Azure-Portal heißt dieses Geheimnis „Schlüssel“, hat jedoch keinen Bezug zu Schlüsseltresoren. Schützen Sie dieses Geheimnis entsprechend. 
     - Die Anwendungs-ID wird später als „AadClientId“ für „Set-AzVMDiskEncryptionExtension“ und als „ServicePrincipalName“ für „Set-AzKeyVaultAccessPolicy“ verwendet. 

## <a name="set-the-key-vault-access-policy-for-the-azure-ad-app"></a>Festlegen der Zugriffsrichtlinie für den Schlüsseltresor für die Azure AD-App
Um Verschlüsselungsgeheimnisse in einen bestimmten Schlüsseltresor zu schreiben, benötigt Azure Disk Encryption die Client-ID und den geheimen Clientschlüssel der Azure Active Directory-Anwendung, berechtigt ist, Geheimnisse in den Schlüsseltresor zu schreiben. 

> [!NOTE]
> Azure Disk Encryption erfordert das Konfigurieren der folgenden Zugriffsrichtlinien für Ihre Azure AD-Clientanwendung: _Wrapkey_- und _Set_-Berechtigungen.

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-powershell"></a>Festlegen der Zugriffsrichtlinie für den Schlüsseltresor für die Azure AD-App mit Azure PowerShell
Ihre Azure AD-Anwendung benötigt Rechte zum Zugreifen auf die Schlüssel oder geheimen Schlüssel im Tresor. Verwenden Sie das Cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy), um der Anwendung Berechtigungen zu erteilen. Verwenden Sie die Client-ID (die bei der Anwendungsregistrierung generiert wurde) als _–ServicePrincipalName_-Parameterwert. Weitere Informationen finden Sie im Blogbeitrag [Azure Key Vault - Step by Step](/archive/blogs/kv/azure-key-vault-step-by-step) (Azure Key Vault – Schritt für Schritt). 

1. Legen Sie die Zugriffsrichtlinie für den Schlüsseltresor für die AD-Anwendung mit PowerShell fest.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-cli"></a>Festlegen der Zugriffsrichtlinie für den Schlüsseltresor für die Azure AD-App mit der Azure-Befehlszeilenschnittstelle
Verwenden Sie [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy), um die Zugriffsrichtlinie festzulegen. Weitere Informationen finden Sie unter [Verwalten von Key Vault mit der CLI 2.0](../../key-vault/general/manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

Erteilen Sie dem über die Azure CLI erstellten Dienstprinzipal Zugriff, damit er Geheimnisse abrufen und Schlüssel mit dem folgenden Befehl packen kann:

```azurecli-interactive
az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-the-portal"></a>Festlegen der Zugriffsrichtlinie für den Schlüsseltresor für die Azure AD-App im Portal

1. Öffnen Sie die Ressourcengruppe mit Ihrem Schlüsseltresor.
2. Wählen Sie Ihren Schlüsseltresor aus, öffnen Sie **Zugriffsrichtlinien**, und klicken Sie dann auf **Neue hinzufügen**.
3. Suchen Sie unter **Prinzipal auswählen** nach der Azure AD-Anwendung, die Sie erstellt haben, und wählen Sie sie aus. 
4. Aktivieren Sie für **Schlüsselberechtigungen** unter **Kryptografische Vorgänge** die Option **Schlüssel packen**.
5. Aktivieren Sie für **Berechtigungen für Geheimnis** unter **Verwaltungsvorgänge für Geheimnisse** die Option **Festlegen**.
6. Klicken Sie auf **OK**, um die Richtlinie zu speichern. 

![Azure Key Vault: „Kryptografische Vorgänge“ und „Schlüssel packen“](../media/disk-encryption/keyvault-portal-fig3.png)

![„Berechtigungen für Geheimnis“ in Azure Key Vault auf „Festlegen“](../media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="set-key-vault-advanced-access-policies"></a>Festlegen von erweiterten Zugriffsrichtlinien für Schlüsseltresore
Die Azure-Plattform benötigt Zugriff auf die Verschlüsselungsschlüssel oder geheimen Schlüssel in Ihrem Schlüsseltresor, um sie für den virtuellen Computer zur Verfügung zu stellen, damit die Volumes gestartet und entschlüsselt werden können. Aktivieren Sie die Datenträgerverschlüsselung im Schlüsseltresor, damit Bereitstellungen nicht fehlschlagen.  

### <a name="set-key-vault-advanced-access-policies-with-azure-powershell"></a>Festlegen der erweiterten Zugriffsrichtlinien für Schlüsseltresore mit Azure PowerShell
 Verwenden Sie das PowerShell-Cmdlet für Schlüsseltresore [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy), um die Datenträgerverschlüsselung für den Schlüsseltresor zu aktivieren.

  - **Aktivieren von Key Vault für die Datenträgerverschlüsselung:** „EnabledForDiskEncryption“ ist für die Verwendung von Azure Disk Encryption erforderlich.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **Aktivieren von Key Vault für die Bereitstellung (falls erforderlich):** Der Ressourcenanbieter „Microsoft.Compute“ wird aktiviert, um Geheimnisse aus diesem Schlüsseltresor abzurufen, wenn dieser Schlüsseltresor bei der Ressourcenerstellung (z. B. beim Erstellen einer VM) referenziert wird.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **Aktivieren von Key Vault für die Vorlagenbereitstellung (falls erforderlich):** Azure Resource Manager kann aus diesem Schlüsseltresor Geheimnisse abrufen, wenn dieser Schlüsseltresor in einer Vorlagenbereitstellung referenziert wird.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="set-key-vault-advanced-access-policies-using-the-azure-cli"></a>Festlegen der erweiterten Zugriffsrichtlinien für Schlüsseltresore mit der Azure-Befehlszeilenschnittstelle
Verwenden Sie [az keyvault update](/cli/azure/keyvault#az-keyvault-update), um die Datenträgerverschlüsselung für den Schlüsseltresor zu aktivieren. 

 - **Aktivieren von Key Vault für die Datenträgerverschlüsselung:** „Enabled-for-disk-encryption“ ist erforderlich. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Aktivieren von Key Vault für die Bereitstellung (falls erforderlich):** Virtuelle Computer können Zertifikate aus dem Schlüsseltresor abrufen, die als Geheimnisse gespeichert sind.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Aktivieren von Key Vault für die Vorlagenbereitstellung (falls erforderlich):** Der Resource Manager kann Geheimnisse aus dem Tresor abrufen.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="set-key-vault-advanced-access-policies-through-the-azure-portal"></a>Festlegen der erweiterten Zugriffsrichtlinien für Schlüsseltresore im Azure-Portal

1. Wählen Sie Ihren Schlüsseltresor aus, navigieren Sie zu **Zugriffsrichtlinien**, und **Klicken Sie, um erweiterte Zugriffsrichtlinien anzuzeigen**.
2. Wählen Sie das Feld **Zugriff auf Azure Disk Encryption für Volumeverschlüsselung aktivieren** aus.
3. Wählen Sie ggf. **Zugriff auf Azure Virtual Machines für Bereitstellung aktivieren** und/oder **Zugriff auf Azure Resource Manager für Vorlagenbereitstellung aktivieren** aus. 
4. Klicken Sie auf **Speichern**.

![Erweiterte Zugriffsrichtlinien für Schlüsseltresore in Azure](../media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-optional"></a>Einrichten eines Schlüsselverschlüsselungsschlüssels (optional)
Wenn Sie Verschlüsselungsschlüssel mit einem Schlüssel für die Schlüsselverschlüsselung zusätzlich schützen möchten, fügen Sie Ihrem Schlüsseltresor einen Schlüsselverschlüsselungsschlüssel hinzu. Verwenden Sie das Cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey), um im Schlüsseltresor einen Schlüsselverschlüsselungsschlüssel zu erstellen. Sie können den KEK auch aus Ihrem lokalen Hardwaresicherheitsmodul (HSM) für die Schlüsselverwaltung importieren. Weitere Informationen finden Sie in der [Key Vault](../../key-vault/keys/hsm-protected-keys.md)-Dokumentation. Wenn ein Schlüsselverschlüsselungsschlüssel angegeben wird, verwendet Azure Disk Encryption diesen, um Verschlüsselungsgeheimnisse vor dem Schreiben in Key Vault zu umschließen. 

* Verwenden Sie beim Generieren von Schlüsseln einen RSA-Schlüsseltyp. Azure Disk Encryption unterstützt noch nicht die Verwendung von Elliptic Curve-Schlüsseln.

* Ihre URLs für das Geheimnis des Schlüsseltresors und den Schlüsselverschlüsselungsschlüssel (Key Encryption Key, KEK) müssen mit einer Versionsangabe versehen sein. Azure erzwingt diese Einschränkung der Versionsverwaltung. Gültige URLs für Geheimnisse und KEKs finden Sie in den folgenden Beispielen:

  * Beispiel für eine gültige Geheimnis-URL: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Beispiel für eine gültige KEK-URL: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Die Angabe von Portnummern als Teil von URLs für Schlüsseltresorgeheimnisse und KEK-URLs wird von Azure Disk Encryption nicht unterstützt. Beispiele für nicht unterstützte und unterstützte Schlüsseltresor-URLs finden Sie hier:

  * Unzulässige Schlüsseltresor-URL: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Zulässige Schlüsseltresor-URL: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="set-up-a-key-encryption-key-with-azure-powershell"></a>Festlegen eines Schlüssels für die Schlüsselverschlüsselung mit Azure PowerShell 
Bevor Sie das PowerShell-Skript verwenden, sollten Sie mit den Voraussetzungen für Azure Disk Encryption vertraut sein, um die Schritte im Skript zu verstehen. Das Beispielskript muss ggf. an Ihre Umgebung angepasst werden. Dieses Skript erstellt alle Voraussetzungen für Azure Disk Encryption und verschlüsselt eine vorhandene IaaS-VM, indem es den Datenträgerschlüssel mit einem Schlüssel für die Schlüsselverschlüsselung umschließt. 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name  $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName  $KVRGname;
     
 #Step 4: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
      $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="certificate-based-authentication-optional"></a>Zertifikatbasierte Authentifizierung (optional)
Wenn Sie Zertifikatauthentifizierung verwenden möchten, können Sie eine in Ihren Schlüsseltresor hochladen und für den Client bereitstellen. Bevor Sie das PowerShell-Skript verwenden, sollten Sie mit den Voraussetzungen für Azure Disk Encryption vertraut sein, um die Schritte im Skript zu verstehen. Das Beispielskript muss ggf. an Ihre Umgebung angepasst werden.

     
 ```powershell

 # Fill in "MyKeyVaultResourceGroup", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
   
   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert".  

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for -EnabledForDeployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM'
   $VMRGName = 'MyVirtualMachineResourceGroup'
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="certificate-based-authentication-and-a-kek-optional"></a>Zertifikatbasierte Authentifizierung und Schlüssel für die Schlüsselverschlüsselung (optional)

Wenn Sie die Zertifikatsauthentifizierung verwenden und den Schlüssel mit einem Schlüsselverschlüsselungsschlüssel umschließen möchten, verwenden Sie das folgende Skript als Beispiel. Bevor Sie das PowerShell-Skript verwenden, sollten Sie mit allen genannten Voraussetzungen für Azure Disk Encryption vertraut sein, um die Schritte im Skript zu verstehen. Das Beispielskript muss ggf. an Ihre Umgebung angepasst werden.

     
 ```powershell
# Fill in 'MyKeyVaultResourceGroup', 'MySecureVault', and 'MyLocation' (if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname

   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert". 

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for deployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM';
   $VMRGName = 'MyVirtualMachineResourceGroup';
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>Nächste Schritte

[Aktivieren von Azure Disk Encryption mit Azure AD auf Windows-VMs (vorheriges Release)](disk-encryption-windows-aad.md)
