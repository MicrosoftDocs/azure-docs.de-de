---
title: Übergeben von Anmeldeinformationen an Azure mithilfe von Desired State Configuration
description: Erfahren Sie, wie Sie Anmeldeinformationen an virtuelle Azure-Computer mithilfe von PowerShell Desired State Configuration (DSC) sicher übergeben.
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: DSC
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: f191ab5819cd69ce90699db60be1665ef77e10a9
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955887"
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Übergeben von Anmeldeinformationen an den Azure DSC-Erweiterungshandler

In diesem Artikel wird die Desired State Configuration-Erweiterung (DSC) für Azure behandelt. Eine Übersicht über den DSC-Erweiterungshandler finden Sie unter [Einführung in den Handler der Azure Desired State Configuration-Erweiterung](dsc-overview.md).

 

## <a name="pass-in-credentials"></a>Übergeben von Anmeldeinformationen

Möglicherweise müssen Sie im Rahmen des Konfigurationsvorgangs Benutzerkonten einrichten, auf Dienste zugreifen oder ein Programm im Benutzerkontext installieren. Um diese Aktionen durchführen zu können, müssen Sie Anmeldeinformationen bereitstellen.

Mit DSC können Sie parametrisierte Konfigurationen einrichten. In einer parametrisierten Konfiguration werden Anmeldeinformationen an die Konfiguration übergeben und sicher in MOF-Dateien gespeichert. Der Azure-Erweiterungshandler vereinfacht die Verwaltung von Anmeldeinformationen, indem er eine automatische Verwaltung von Zertifikaten bereitstellt.

Das folgende DSC-Konfigurationsskript erstellt ein lokales Benutzerkonto mit dem angegebenen Kennwort:

```powershell
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )
    Node localhost {
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        }
    }
}
```

Es ist wichtig, **node localhost** in die Konfiguration einzuschließen. Der Erweiterungshandler sucht insbesondere nach der **node localhost**-Anweisung. Wenn diese Anweisung nicht vorhanden ist, funktionieren die folgenden Schritte nicht. Außerdem muss die Typumwandlung **[PsCredential]** eingeschlossen werden. Dieser spezifische Typ löst die Verschlüsselung der Anmeldeinformationen durch die Erweiterung aus.

So veröffentlichen Sie dieses Skript in Azure Blob Storage

`Publish-AzVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

So richten Sie die Azure DSC-Erweiterung ein und stellen die Anmeldeinformationen bereit

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzVM -Name 'example-1'

$vm = Set-AzVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzVM
```

## <a name="how-a-credential-is-secured"></a>Schützen von Anmeldeinformationen

Auf das Ausführen dieses Codes folgt die Aufforderung, Anmeldeinformationen anzugeben. Nachdem die Anmeldeinformationen bereitgestellt sind, werden sie kurz im Arbeitsspeicher gespeichert. Beim Veröffentlichen der Anmeldeinformationen mithilfe des Cmdlets **Set-AzVMDscExtension** werden sie über HTTPS an den virtuellen Computer übertragen. Auf dem virtuellen Computer speichert Azure die Anmeldeinformationen verschlüsselt über das lokale VM-Zertifikat auf dem Datenträger. Die Anmeldeinformationen werden dann für kurze Zeit im Arbeitsspeicher entschlüsselt und wieder verschlüsselt, um an DSC übergeben zu werden.

Dieser Vorgang unterscheidet sich von der [Verwendung von sicheren Konfigurationen ohne den Erweiterungshandler](/powershell/scripting/dsc/pull-server/securemof). Die Azure-Umgebung bietet eine Möglichkeit zum sicheren Übertragen von Konfigurationsdaten über Zertifikate. Mit dem DSC-Erweiterungshandler besteht keine Notwendigkeit, **$CertificatePath** oder einen Eintrag **$CertificateID**/  **$Thumbprint** in **ConfigurationData** anzugeben.

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie die [Einführung in den Azure DSC-Erweiterungshandler](dsc-overview.md).
- Sehen Sie sich die [Azure Resource Manager-Vorlage für die DSC-Erweiterung](dsc-template.md)an.
- Weitere Informationen zu PowerShell DSC finden Sie im [PowerShell-Dokumentationscenter](/powershell/scripting/dsc/overview/overview).
- Durchsuchen Sie für mehr Funktionalität, die Sie mithilfe von PowerShell DSC verwalten können, und für weitere DSC-Ressourcen den [PowerShell-Katalog](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).