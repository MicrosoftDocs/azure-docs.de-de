---
title: Azure Key Vault-VM-Erweiterung für Windows
description: Es wird beschrieben, wie Sie einen Agent zur automatischen Aktualisierung von Key Vault-Geheimnissen auf virtuellen Computern per VM-Erweiterung bereitstellen.
services: virtual-machines-windows
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 0418c11d84fb82ac4a4b181289f032407a29f92e
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500664"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Key Vault-VM-Erweiterung für Windows

Die Key Vault-VM-Erweiterung ermöglicht die automatische Aktualisierung von Zertifikaten, die in einem Azure-Schlüsseltresor gespeichert sind. Hierbei überwacht die Erweiterung eine Liste mit beobachteten Zertifikaten, die in Schlüsseltresoren gespeichert sind, und ruft bei Erkennung einer Änderung die entsprechenden Zertifikate ab und installiert sie. Dieses Dokument enthält ausführliche Informationen zu den unterstützten Plattformen, Konfigurationen und Bereitstellungsoptionen für die Key Vault-VM-Erweiterung für Windows. 

### <a name="operating-system"></a>Betriebssystem

Die Key Vault-VM-Erweiterung unterstützt folgende Windows-Versionen:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012

Die Key Vault-VM-Erweiterung wird auch auf einer benutzerdefinierten lokalen VM unterstützt, die hochgeladen und mithilfe der Windows Server 2019-Kerninstallation in ein spezielles Image zur Verwendung in Azure konvertiert wird.

### <a name="supported-certificate-content-types"></a>Unterstützte Zertifikatsinhaltstypen

- PKCS #12
- PEM

## <a name="prerequisities"></a>Voraussetzungen
  - Key Vault-Instanz mit Zertifikat. Siehe [Erstellen eines Schlüsseltresors](../../key-vault/general/quick-create-portal.md).
  - Der VM/VMSS muss eine [verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md) zugewiesen sein.
  - Die Key Vault-Zugriffsrichtlinie muss mit geheimen Schlüsseln `get` und `list` Berechtigung für die verwaltete VM/VMSS-Identität festgelegt werden, um den Teil des Zertifikats für den geheimen Schlüssel abzurufen. Weitere Informationen finden Sie unter [Authentifizieren bei Key Vault](../../key-vault/general/authentication.md) und [Zuweisen einer Key Vault-Zugriffsrichtlinie](../../key-vault/general/assign-access-policy-cli.md).

## <a name="extension-schema"></a>Erweiterungsschema

Im folgenden JSON-Code ist das Schema für die Key Vault-VM-Erweiterung dargestellt. Für die Erweiterung sind keine geschützten Einstellungen erforderlich. Alle Einstellungen werden als öffentliche Informationen betrachtet. Für die Erweiterung werden eine Liste mit berücksichtigten Zertifikaten, die Abrufhäufigkeit und ein Zielzertifikatspeicher benötigt. Dies gilt insbesondere in folgenden Fällen:  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature ensures s-channel binding when certificate renews, without necessitating a re-deployment.  e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
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
| type | KeyVaultForWindows | Zeichenfolge |
| typeHandlerVersion | 1.0 | INT |
| pollingIntervalInS | 3600 | Zeichenfolge |
| certificateStoreName | MY | Zeichenfolge |
| linkOnRenewal | false | boolean |
| certificateStoreLocation  | LocalMachine oder CurrentUser (Groß-/Kleinschreibung beachten) | Zeichenfolge |
| requiredInitialSync | true | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate","https://myvault.vault.azure.net/secrets/mycertificate2"] | Zeichenfolgenarray
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
      "name": "KeyVaultForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"]>
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
        $extName =  "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
       
    
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
        $extName = "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
        
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
         az vm extension set -name "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -resource-group "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

* So stellen Sie die Erweiterung in einer VM-Skalierungsgruppe bereit:

   ```azurecli
        # Start the deployment
        az vmss extension set -name "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -resource-group "<resourcegroup>" `
         --vmss-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

Beachten Sie hierbei die folgenden Einschränkungen bzw. Anforderungen:
- Key Vault-Einschränkungen:
  - Muss zum Zeitpunkt der Bereitstellung vorhanden sein. 
  - Die Key Vault-Zugriffsrichtlinie muss für die VM/VMSS-Identität mithilfe einer verwalteten Identität festgelegt werden. Weitere Informationen finden Sie unter [Authentifizieren bei Key Vault](../../key-vault/general/authentication.md) und [Zuweisen einer Key Vault-Zugriffsrichtlinie](../../key-vault/general/assign-access-policy-cli.md).

## <a name="troubleshoot-and-support"></a>Problembehandlung und Support

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
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```

### <a name="support"></a>Support

Sollten Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie sich über das [MSDN Azure-Forum oder über das Stack Overflow-Forum](https://azure.microsoft.com/support/forums/) mit Azure-Experten in Verbindung setzen. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie „Support erhalten“ aus. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](https://azure.microsoft.com/support/faq/).