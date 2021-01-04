---
title: Azure Disk Encryption für Linux
description: Stellen Sie Azure Disk Encryption mithilfe einer VM-Erweiterung auf einem virtuellen Linux-Computer bereit.
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: 61f8fd3d671bbd66d2dc5656e9a467b06798d280
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968416"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption für Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Übersicht

Azure Disk Encryption nutzt das Subsystem dm-crypt in Linux zur Gewährleistung einer vollständigen Datenträgerverschlüsselung bei der [Auswahl von Azure Linux-Verteilungen](../linux/disk-encryption-overview.md).  Diese Lösung ist in Azure Key Vault integriert, um die Verwaltung der Datenträger-Verschlüsselungsschlüssel zu erleichtern.

## <a name="prerequisites"></a>Voraussetzungen

Eine vollständige Liste mit den Voraussetzungen finden Sie unter [Azure Disk Encryption für Linux-VMs](../linux/disk-encryption-overview.md). Achten Sie besonders auf die folgenden Abschnitte:

- [Unterstützte VMs und Betriebssysteme](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Weitere VM-Anforderungen](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Netzwerkanforderungen](../linux/disk-encryption-overview.md#networking-requirements)
- [Speicheranforderungen für Verschlüsselungsschlüssel](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>Erweiterungsschema

Es gibt zwei Versionen des Erweiterungsschemas für Azure Disk Encryption (ADE):
- v1.1 – Ein neueres empfohlenes Schema, das keine Azure Active Directory (AAD)-Eigenschaften verwendet.
- v0.1 – Ein älteres Schema, für das keine Azure Active Directory (AAD)-Eigenschaften erforderlich sind. 

Um ein Zielschema auszuwählen, muss die `typeHandlerVersion`-Eigenschaft auf die Version des Schemas festgelegt werden, das Sie verwenden möchten.

### <a name="schema-v11-no-aad-recommended"></a>Schema v1.1: Kein AAD (empfohlen)

Das v1.1-Schema wird empfohlen und erfordert keine Azure Active Directory (AAD)-Eigenschaften.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "1.1",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[KeyVaultResourceId]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[KekVaultResourceId",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v01-with-aad"></a>Schema v0.1: mit AAD 

Das 0.1-Schema erfordert `AADClientID` und entweder `AADClientSecret` oder `AADClientCertificate`.

Verwenden von `AADClientSecret`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
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
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
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
| type | AzureDiskEncryptionForLinux | Zeichenfolge |
| typeHandlerVersion | 1.1, 0.1 | INT |
| (0.1-Schema) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (0.1-Schema) AADClientSecret | password | Zeichenfolge |
| (0.1-Schema) AADClientCertificate | thumbprint | Zeichenfolge |
| (optional) (0.1-Schema) Passphrase | password | Zeichenfolge |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON-Wörterbuch |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | Zeichenfolge | 
| (optional – Standard-RSA-OAEP) KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | Zeichenfolge |
| KeyVaultURL | url | Zeichenfolge |
| KeyVaultResourceId | url | Zeichenfolge |
| (optional) KeyEncryptionKeyURL | url | Zeichenfolge |
| (optional) KekVaultResourceId | url | Zeichenfolge |
| (optional) SequenceVersion | UNIQUEIDENTIFIER | Zeichenfolge |
| VolumeType | Betriebssystem, Daten, alle | Zeichenfolge |

## <a name="template-deployment"></a>Bereitstellung von Vorlagen

Ein Beispiel für eine Vorlagenbereitstellung, die auf Schema v1.1 basiert, finden Sie in der Azure-Schnellstartvorlage [201-encrypt-running-linux-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

Ein Beispiel für eine Vorlagenbereitstellung, die auf Schema v0.1 basiert, finden Sie in der Azure-Schnellstartvorlage [201-encrypt-running-linux-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

>[!WARNING]
> - Wenn Sie zuvor Azure Disk Encryption mit Azure AD zum Verschlüsseln eines virtuellen Computers verwendet haben, müssen Sie diese Option auch weiterhin zum Verschlüsseln Ihres virtuellen Computers verwenden.
> - Beim Verschlüsseln von Linux-Betriebssystemvolumes sollte die VM als nicht verfügbar angesehen werden. Es wird dringend empfohlen, SSH-Anmeldungen zu vermeiden, während die Verschlüsselung ausgeführt wird, damit es nicht aufgrund von Problemen zur Blockierung geöffneter Dateien kommt, auf die während des Verschlüsselungsvorgangs zugegriffen werden muss. Verwenden Sie zum Überprüfen des Fortschritts das PowerShell-Cmdlet [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) oder den CLI-Befehl [vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show). Es ist zu erwarten, dass dieser Prozess bei einem Betriebssystemvolume mit 30 GB einige Stunden in Anspruch nimmt, zuzüglich der Zeit für die Verschlüsselung von Datenvolumes. Die Verschlüsselungszeit für das Datenvolume hängt proportional von der Größe und Menge der Datenvolumes ab, es sei denn, es wird die Verschlüsselungsformatoption „all“ verwendet. 
> - Das Deaktivieren der Verschlüsselung auf virtuellen Linux-Computern wird nur für Datenvolumes unterstützt. Dies wird nicht auf Daten- oder Betriebssystemvolumes unterstützt, wenn das Betriebssystemvolume verschlüsselt wurde. 

>[!NOTE]
> Wenn der `VolumeType`-Parameter auf „All“ festgelegt ist, werden Datenträger nur dann verschlüsselt, wenn sie ordnungsgemäß eingebunden sind.

## <a name="troubleshoot-and-support"></a>Problembehandlung und Support

### <a name="troubleshoot"></a>Problembehandlung

Informationen zur Problembehandlung finden Sie unter [Leitfaden zur Azure Disk Encryption-Problembehandlung](../linux/disk-encryption-troubleshooting.md).

### <a name="support"></a>Support

Sollten Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie sich über das [MSDN Azure-Forum oder über das Stack Overflow-Forum](https://azure.microsoft.com/support/community/) mit Azure-Experten in Verbindung setzen. 

Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Navigieren Sie zum [Azure-Support](https://azure.microsoft.com/support/options/), und wählen Sie „Support erhalten“ aus. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu VM-Erweiterungen finden Sie unter [Erweiterungen und Features für virtuelle Computer für Linux](features-linux.md).
* Weitere Informationen zu Azure Disk Encryption für Linux finden Sie unter [Virtuelle Linux-Computer](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines).