---
title: Azure Key Vault-VM-Erweiterung für Linux
description: Hier erfahren Sie, wie Sie auf virtuellen Computern mithilfe einer VM-Erweiterung einen Agent zur automatischen Aktualisierung von Key Vault-Zertifikaten bereitstellen.
services: virtual-machines-linux
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 9377a21415c22e7b68d850ca1a95f931e62fe573
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499729"
---
# <a name="key-vault-virtual-machine-extension-for-linux"></a>Key Vault-VM-Erweiterung für Linux

Die Key Vault-VM-Erweiterung ermöglicht die automatische Aktualisierung von Zertifikaten, die in einem Azure-Schlüsseltresor gespeichert sind. Die Erweiterung überwacht insbesondere eine Liste der in Schlüsseltresoren gespeicherten, berücksichtigten Zertifikate.  Bei Erkennung einer Änderung ruft die Erweiterung die entsprechenden Zertifikate ab und installiert sie. Die Key Vault-VM-Erweiterung wird von Microsoft veröffentlicht und unterstützt (derzeit auf virtuellen Linux-Computern). Dieses Dokument enthält ausführliche Informationen zu den unterstützten Plattformen, Konfigurationen und Bereitstellungsoptionen für die Key Vault-VM-Erweiterung für Linux. 

### <a name="operating-system"></a>Betriebssystem

Die Key Vault-VM-Erweiterung unterstützt folgende Linux-Distributionen:

- Ubuntu-1604
- Ubuntu-1804
- Debian-9
- Suse-15 

### <a name="supported-certificate-content-types"></a>Unterstützte Zertifikatsinhaltstypen

- PKCS #12
- PEM

## <a name="prerequisities"></a>Voraussetzungen
  - Key Vault-Instanz mit Zertifikat. Siehe [Erstellen eines Schlüsseltresors](../../key-vault/general/quick-create-portal.md).
  - Der VM/VMSS muss eine [verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md) zugewiesen sein.
  - Die Key Vault-Zugriffsrichtlinie muss mit geheimen Schlüsseln `get` und `list` Berechtigung für die verwaltete VM/VMSS-Identität festgelegt werden, um den Teil des Zertifikats für den geheimen Schlüssel abzurufen. Weitere Informationen finden Sie unter [Authentifizieren bei Key Vault](../../key-vault/general/authentication.md) und [Zuweisen einer Key Vault-Zugriffsrichtlinie](../../key-vault/general/assign-access-policy-cli.md).

## <a name="extension-schema"></a>Erweiterungsschema

Im folgenden JSON-Code ist das Schema für die Key Vault-VM-Erweiterung dargestellt. Für die Erweiterung sind keine geschützten Einstellungen erforderlich. Alle Einstellungen werden als Informationen ohne Sicherheitsauswirkungen angesehen. Für die Erweiterung werden eine Liste mit überwachten Geheimnissen, die Abrufhäufigkeit und ein Zielzertifikatspeicher benötigt. Dies gilt insbesondere in folgenden Fällen:  
```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <It is ignored on Linux>,
          "linkOnRenewal": <Not available on Linux e.g.: false>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"]>
        },
        "authenticationSettings": {
                "msiEndpoint":  <Optional MSI endpoint e.g.: "http://169.254.169.254/metadata/identity">,
                "msiClientId":  <Optional MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
       }
      }
    }
```

> [!NOTE]
> Die URLs der berücksichtigten Zertifikate müssen das folgende Format haben: `https://myVaultName.vault.azure.net/secrets/myCertName`.
> 
> Der Grund: Der Pfad `/secrets` gibt das vollständige Zertifikat einschließlich des privaten Schlüssels zurück, der Pfad `/certificates` dagegen nicht. Weitere Informationen zu Zertifikaten finden Sie hier: [Key Vault-Zertifikate](../../key-vault/general/about-keys-secrets-certificates.md)

> [!IMPORTANT]
> Die Eigenschaft „authenticationSettings“ ist nur **erforderlich**, wenn Sie VMs mit **benutzerseitig zugewiesenen Identitäten** verwenden.
> Sie gibt die Identität an, die für die Authentifizierung bei Key Vault verwendet werden soll.


### <a name="property-values"></a>Eigenschaftswerte

| Name | Wert/Beispiel | Datentyp |
| ---- | ---- | ---- |
| apiVersion | 01.07.2019 | date |
| publisher | Microsoft.Azure.KeyVault | Zeichenfolge |
| type | KeyVaultForLinux | Zeichenfolge |
| typeHandlerVersion | 1.0 | INT |
| pollingIntervalInS | 3600 | Zeichenfolge |
| certificateStoreName | Wird unter Linux ignoriert | Zeichenfolge |
| linkOnRenewal | false | boolean |
| certificateStoreLocation  | /var/lib/waagent/Microsoft.Azure.KeyVault | Zeichenfolge |
| requiredInitialSync | true | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"] | Zeichenfolgenarray
| msiEndpoint | http://169.254.169.254/metadata/identity | Zeichenfolge |
| msiClientId | c7373ae5-91c2-4165-8ab6-7381d6e75619 | Zeichenfolge |


## <a name="template-deployment"></a>Bereitstellung von Vorlagen

Azure-VM-Erweiterungen können mithilfe von Azure Resource Manager-Vorlagen bereitgestellt werden. Vorlagen sind ideal, wenn Sie virtuelle Computer bereitstellen, für die nach der Bereitstellung eine Aktualisierung der Zertifikate erforderlich ist. Die Erweiterung kann auf einzelnen virtuellen Computern oder in VM-Skalierungsgruppen bereitgestellt werden. Das Schema und die Konfiguration sind für beide Vorlagentypen gleich. 

Die JSON-Konfiguration für eine VM-Erweiterung muss im VM-Ressourcenfragment der Vorlage geschachtelt sein – genauer gesagt im Objekt `"resources": []` für die VM-Vorlage und im Falle einer VM-Skalierungsgruppe unter dem Objekt `"virtualMachineProfile":"extensionProfile":{"extensions" :[]`.

 > [!NOTE]
> Die VM-Erweiterung erfordert, dass eine system- oder benutzerverwaltete Identität für die Authentifizierung beim Schlüsseltresor zugewiesen wird.  Weitere Informationen finden Sie unter [Authentifizieren bei Key Vault und Zuweisen einer Key Vault-Zugriffsrichtlinie](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md).
> 

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
          "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <ingnored on linux>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```


## <a name="azure-powershell-deployment"></a>Azure PowerShell-Bereitstellung
> [!WARNING]
> PowerShell-Clients fügen `"` häufig `\` in der Datei „settings.json“ hinzu. Dies verursacht bei akvvm_service folgenden Fehler: `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

Azure PowerShell kann verwendet werden, um die Key Vault-VM-Erweiterung auf einem vorhandenen virtuellen Computer oder in einer VM-Skalierungsgruppe bereitzustellen. 

* Stellen Sie die Erweiterung wie folgt auf einer VM bereit:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName =  "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* So stellen Sie die Erweiterung in einer VM-Skalierungsgruppe bereit:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName = "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Bereitstellung mithilfe der Azure-Befehlszeilenschnittstelle

Die Azure-Befehlszeilenschnittstelle kann verwendet werden, um die Key Vault-VM-Erweiterung auf einem vorhandenen virtuellen Computer oder in einer VM-Skalierungsgruppe bereitzustellen. 
 
* Stellen Sie die Erweiterung wie folgt auf einer VM bereit:
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForLinux" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

* So stellen Sie die Erweiterung in einer VM-Skalierungsgruppe bereit:

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForLinux" `
        --publisher Microsoft.Azure.KeyVault `
        -g "<resourcegroup>" `
        --vm-name "<vmName>" `
        --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```
Beachten Sie hierbei die folgenden Einschränkungen bzw. Anforderungen:
- Key Vault-Einschränkungen:
  - Muss zum Zeitpunkt der Bereitstellung vorhanden sein. 
  - Die Key Vault-Zugriffsrichtlinie muss für die VM/VMSS-Identität mithilfe einer verwalteten Identität festgelegt werden. Weitere Informationen finden Sie unter [Authentifizieren bei Key Vault](../../key-vault/general/authentication.md) und [Zuweisen einer Key Vault-Zugriffsrichtlinie](../../key-vault/general/assign-access-policy-cli.md).

### <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

* Gibt es einen Grenzwert für die Anzahl von „observedCertificates“, die Sie einrichten können?
  Nein, die Key Vault-VM-Erweiterung weist keinen Grenzwert für die Anzahl von „observedCertificates“ auf.


### <a name="troubleshoot"></a>Problembehandlung

Daten zum Status von Erweiterungsbereitstellungen können über das Azure-Portal und mithilfe von Azure PowerShell abgerufen werden. Führen Sie über Azure PowerShell den folgenden Befehl aus, um den Bereitstellungsstatus von Erweiterungen für eine bestimmte VM anzuzeigen.

**Azure PowerShell**
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

**Azure-Befehlszeilenschnittstelle**
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```
#### <a name="logs-and-configuration"></a>Protokolle und Konfiguration

```
/var/log/waagent.log
/var/log/azure/Microsoft.Azure.KeyVault.KeyVaultForLinux/*
/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-<most recent version>/config/*
```
### <a name="using-symlink"></a>Verwenden von Symlink

Symbolische Verknüpfungen, auch als Symlinks bezeichnet, sind erweiterte Verknüpfungen. Damit Sie den Ordner nicht überwachen müssen und automatisch das neueste Zertifikat erhalten, können Sie den Symlink `([VaultName].[CertificateName])` verwenden, um die neueste Version des Zertifikats unter Linux abzurufen.

### <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

* Gibt es einen Grenzwert für die Anzahl von „observedCertificates“, die Sie einrichten können?
  Nein, die Key Vault-VM-Erweiterung weist keinen Grenzwert für die Anzahl von „observedCertificates“ auf.

### <a name="support"></a>Support

Sollten Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie sich über das [MSDN Azure-Forum oder über das Stack Overflow-Forum](https://azure.microsoft.com/support/forums/) mit Azure-Experten in Verbindung setzen. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie „Support erhalten“ aus. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](https://azure.microsoft.com/support/faq/).