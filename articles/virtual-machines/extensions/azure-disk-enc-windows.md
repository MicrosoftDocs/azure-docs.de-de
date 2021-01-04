---
title: Azure Disk Encryption für Windows
description: Stellen Sie Azure Disk Encryption mithilfe einer VM-Erweiterung auf einem virtuellen Windows-Computer bereit.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: e5a0069e02c5285a950d23abc0ec4bee6e9e467b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968382"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Azure Disk Encryption für Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Übersicht

Azure Disk Encryption nutzt BitLocker, um auf virtuellen Azure-Computern unter Windows eine vollständige Datenträgerverschlüsselung bereitzustellen.  Diese Lösung ist in Azure Key Vault integriert, um die Verschlüsselungsschlüssel und Geheimnisse für die Datenträgerverschlüsselung in Ihrem Key Vault-Abonnement zu verwalten. 

## <a name="prerequisites"></a>Voraussetzungen

Eine vollständige Liste mit den Voraussetzungen finden Sie unter [Azure Disk Encryption für virtuelle Windows-Computer](../windows/disk-encryption-overview.md). Achten Sie besonders auf die folgenden Abschnitte:

- [Unterstützte VMs und Betriebssysteme](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Netzwerkanforderungen](../windows/disk-encryption-overview.md#networking-requirements)
- [Gruppenrichtlinienanforderungen](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schema"></a>Erweiterungsschema

Es gibt zwei Versionen des Erweiterungsschemas für Azure Disk Encryption (ADE):
- v2.2: Ein neueres empfohlenes Schema, das keine AAD-Eigenschaften (Azure Active Directory) verwendet.
- v1.1: Ein älteres Schema, für das AAD-Eigenschaften (Azure Active Directory) erforderlich sind. 

Zum Auswählen eines Zielschemas muss die Eigenschaft `typeHandlerVersion` auf die gewünschte Schemaversion festgelegt werden.

### <a name="schema-v22-no-aad-recommended"></a>Schema v2.2: Kein AAD (empfohlen)

Das v2.2-Schema wird für alle neuen virtuellen Computer empfohlen und erfordert keine Azure Active Directory-Eigenschaften.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryption",
        "typeHandlerVersion": "2.2",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KekVaultResourceId": "[keyVaultResourceID]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultResourceId": "[keyVaultResourceID]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v11-with-aad"></a>Schema v1.1: mit AAD 

Das 1.1-Schema erfordert `aadClientID` und entweder `aadClientSecret` oder `AADClientCertificate` und wird für neue virtuelle Computer nicht empfohlen.

Verwenden von `aadClientSecret`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```

Verwenden von `AADClientCertificate`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>Eigenschaftswerte

| Name | Wert/Beispiel | Datentyp |
| ---- | ---- | ---- |
| apiVersion | 01.07.2019 | date |
| publisher | Microsoft.Azure.Security | Zeichenfolge |
| type | AzureDiskEncryption | Zeichenfolge |
| typeHandlerVersion | 2.2, 1.1 | Zeichenfolge |
| (1.1-Schema) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (1.1-Schema) AADClientSecret | password | Zeichenfolge |
| (1.1-Schema) AADClientCertificate | thumbprint | Zeichenfolge |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | Zeichenfolge | 
| (optional – Standard-RSA-OAEP) KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | Zeichenfolge |
| KeyVaultURL | url | Zeichenfolge |
| KeyVaultResourceId | url | Zeichenfolge |
| (optional) KeyEncryptionKeyURL | url | Zeichenfolge |
| (optional) KekVaultResourceId | url | Zeichenfolge |
| (optional) SequenceVersion | uniqueidentifier | Zeichenfolge |
| VolumeType | Betriebssystem, Daten, alle | Zeichenfolge |

## <a name="template-deployment"></a>Bereitstellung von Vorlagen

Ein Beispiel für eine auf der Schemaversion 2.2 basierende Vorlagenbereitstellung finden Sie in der Azure-Schnellstartvorlage [201-encrypt-running-windows-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).

Ein Beispiel für eine auf der Schemaversion 1.1 basierende Vorlagenbereitstellung finden Sie in der Azure-Schnellstartvorlage [201-encrypt-running-windows-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).

>[!NOTE]
> Wenn der Parameter `VolumeType` auf „All“ festgelegt ist, werden Datenträger nur dann verschlüsselt, wenn sie ordnungsgemäß formatiert sind. 

## <a name="troubleshoot-and-support"></a>Problembehandlung und Support

### <a name="troubleshoot"></a>Problembehandlung

Informationen zur Problembehandlung finden Sie unter [Leitfaden zur Azure Disk Encryption-Problembehandlung](../windows/disk-encryption-troubleshooting.md).

### <a name="support"></a>Support

Sollten Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie sich über das [MSDN Azure-Forum oder über das Stack Overflow-Forum](https://azure.microsoft.com/support/community/) mit Azure-Experten in Verbindung setzen. 

Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Navigieren Sie zum [Azure-Support](https://azure.microsoft.com/support/options/), und wählen Sie „Support erhalten“ aus. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Erweiterungen finden Sie unter [Erweiterungen und Features für virtuelle Computer für Windows](features-windows.md).
* Weitere Informationen zu Azure Disk Encryption für Windows finden Sie unter [Virtuelle Windows-Computer](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#windows-virtual-machines).
