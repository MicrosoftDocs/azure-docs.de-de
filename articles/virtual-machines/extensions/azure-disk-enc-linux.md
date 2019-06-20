---
title: Azure Disk Encryption für Linux | Microsoft-Dokumentation
description: Stellen Sie Azure Disk Encryption mithilfe einer VM-Erweiterung auf einem virtuellen Linux-Computer bereit.
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 36e8875e91e2f04dbb60bab3211f07b2053e78f5
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414771"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption für Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Übersicht

Azure Disk Encryption nutzt das Subsystem dm-crypt in Linux zur Gewährleistung einer vollständigen Datenträgerverschlüsselung bei der [Auswahl von Azure Linux-Verteilungen](https://aka.ms/adelinux).  Diese Lösung ist in Azure Key Vault integriert, um die Verwaltung der Datenträger-Verschlüsselungsschlüssel zu erleichtern.

## <a name="prerequisites"></a>Voraussetzungen

Eine vollständige Liste der Voraussetzungen finden Sie unter [Voraussetzungen für Azure Disk Encryption](
../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="operating-system"></a>Betriebssystem

Azure Disk Encryption wird für die folgenden ausgewählten Verteilungen und Versionen unterstützt.  Die Liste der unterstützten Linux-Verteilungen finden Sie unter [Häufig gestellte Fragen zu Azure Disk Encryption](../../security/azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport).

### <a name="internet-connectivity"></a>Internetkonnektivität

Für Azure Disk Encryption für Linux ist eine Internetverbindung für den Zugriff auf Active Directory, Key Vault, Storage und Paketverwaltungs-Endpunkte erforderlich.  Weitere Informationen finden Sie unter [Voraussetzungen für Azure Disk Encryption](../../security/azure-security-disk-encryption-prerequisites.md).

## <a name="extension-schema"></a>Erweiterungsschema

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```

### <a name="property-values"></a>Eigenschaftswerte

| NAME | Wert/Beispiel | Datentyp |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | Zeichenfolge |
| type | AzureDiskEncryptionForLinux | Zeichenfolge |
| typeHandlerVersion | 0.1, 1.1 (VMSS) | int |
| AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID | 
| AADClientSecret | password | Zeichenfolge |
| AADClientCertificate | thumbprint | Zeichenfolge |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON-Wörterbuch |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | Zeichenfolge | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | Zeichenfolge |
| KeyEncryptionKeyURL | URL | Zeichenfolge |
| KeyVaultURL | URL | Zeichenfolge |
| Passphrase | password | Zeichenfolge | 
| SequenceVersion | uniqueidentifier | Zeichenfolge |
| VolumeType | Betriebssystem, Daten, alle | Zeichenfolge |

## <a name="template-deployment"></a>Bereitstellung von Vorlagen

Ein Beispiel für die Bereitstellung von Vorlagen finden Sie unter [Aktivieren der Verschlüsselung auf einem laufenden virtuellen Linux-Computer](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

## <a name="azure-cli-deployment"></a>Bereitstellung mithilfe der Azure-Befehlszeilenschnittstelle

Anweisungen finden Sie in der aktuellen [Dokumentation der Azure-Befehlszeilenschnittstelle](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Problembehandlung und Support

### <a name="troubleshoot"></a>Problembehandlung

Informationen zur Problembehandlung finden Sie unter [Leitfaden zur Azure Disk Encryption-Problembehandlung](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Support

Sollten Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie sich über das [MSDN Azure-Forum oder über das Stack Overflow-Forum](https://azure.microsoft.com/support/community/) mit Azure-Experten in Verbindung setzen. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie „Support erhalten“ aus. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu VM-Erweiterungen finden Sie unter [Erweiterungen und Features für virtuelle Computer für Linux](features-linux.md).
