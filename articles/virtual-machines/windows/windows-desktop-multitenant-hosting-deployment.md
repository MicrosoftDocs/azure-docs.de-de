---
title: Informationen zum Bereitstellen von Windows 10 unter Azure mit mehrinstanzenfähigen Hostingrechten
description: Erfahren Sie, wie Sie die Vorteile von Windows Software Assurance optimal nutzen, um lokale Lizenzen in Azure mit mehrinstanzenfähigen Hostingrechten zu verwenden.
author: mimckitt
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 2/2/2021
ms.author: mimckitt
ms.custom: rybaker, chmimckitt
ms.openlocfilehash: 744f265251e9f58ce91085f0e5dd5d1ad13eec69
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625110"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Informationen zum Bereitstellen von Windows 10 unter Azure mit mehrinstanzenfähigen Hostingrechten 
Für Kunden mit Windows 10 Enterprise E3/E5 pro Benutzer oder Windows Virtual Desktop Access pro Benutzer (Benutzerabonnementlizenzen oder Add-On-Benutzerabonnementlizenzen) können Sie Ihre Windows 10-Lizenzen mittels mehrinstanzenfähiger Hostingrechte für Windows 10 in der Cloud verwenden und virtuelle Windows 10-Computer in Azure ausführen, ohne eine weitere Lizenz erwerben zu müssen. Rechte für das Hosten mehrerer Mandanten sind nur für Windows 10 (Version 1703 oder höher) verfügbar.

Weitere Informationen finden Sie unter [Mehrinstanzenfähiges Hosting für Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> - Weitere Informationen zur Verwendung von Images für Windows 7, Windows 8.1 und Windows 10 für die Entwicklung oder für Tests finden Sie unter [Verwenden des Windows-Clients in Azure für Entwicklungs-/Testszenarien](client-images.md).
> - Die Vorteile der Windows Server-Lizenzierung werden unter [Azure-Hybridvorteil für Windows Server](hybrid-use-benefit-licensing.md) erläutert.

## <a name="subscription-licenses-that-qualify-for-multitenant-hosting-rights"></a>Abonnementlizenzen, die sich für das Hosten mehrerer Mandanten qualifizieren

Mithilfe des [Microsoft Admin Centers](https://docs.microsoft.com/microsoft-365/admin/admin-overview/about-the-admin-center?view=o365-worldwide&preserve-view=true) können Sie überprüfen, ob einem Benutzer eine von Windows 10 unterstützte Lizenz zugewiesen wurde.

> [!IMPORTANT]
> Benutzer müssen über eine der unten aufgeführten Abonnementlizenzen verfügen, um Windows 10-Images in Azure verwenden zu können. Wenn Sie nicht über eine dieser Abonnementlizenzen verfügen, können Sie diese über Ihren [Cloud Service Partner](https://azure.microsoft.com/overview/choosing-a-cloud-service-provider/) oder direkt über [Microsoft](https://www.microsoft.com/microsoft-365?rtc=1) erwerben.

**Berechtigte Abonnementlizenzen:**

-   Microsoft 365 E3/E5 
-   Microsoft 365 F3 
-   Microsoft 365 A3/A5 
-   Windows 10 Enterprise E3/E5
-   Windows 10 Education A3/A5 
-   Windows VDA E3/E5


## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Bereitstellen des Windows 10-Image aus dem Azure Marketplace 
Für PowerShell-, CLI- und Azure Resource Manager-Vorlagenbereitstellungen können Windows 10-Images mithilfe von `PublisherName: MicrosoftWindowsDesktop` und `Offer: Windows-10` gefunden werden. Windows 10 Version Creators Update (1809) oder höher wird für die Rechte zum Hosten mehrerer Mandanten unterstützt. 

```powershell
Get-AzVmImageSku -Location '$location' -PublisherName 'MicrosoftWindowsDesktop' -Offer 'Windows-10'

Skus                        Offer      PublisherName           Location 
----                        -----      -------------           -------- 
rs4-pro                     Windows-10 MicrosoftWindowsDesktop eastus   
rs4-pron                    Windows-10 MicrosoftWindowsDesktop eastus   
rs5-enterprise              Windows-10 MicrosoftWindowsDesktop eastus   
rs5-enterprisen             Windows-10 MicrosoftWindowsDesktop eastus   
rs5-pro                     Windows-10 MicrosoftWindowsDesktop eastus   
rs5-pron                    Windows-10 MicrosoftWindowsDesktop eastus  
```

Weitere Informationen zu verfügbaren Images finden Sie unter [Suchen und Verwenden von VM-Images im Azure Marketplace mit Azure PowerShell](./cli-ps-findimage.md).

## <a name="uploading-windows-10-vhd-to-azure"></a>Hochladen der Windows 10-VHD in Azure
Beachten Sie beim Hochladen einer generalisierten Windows 10-VHD, dass das integrierte Administratorkonto bei Windows 10 standardmäßig nicht aktiviert ist. Um das integrierte Administratorkonto zu aktivieren, schließen Sie den folgenden Befehl als Teil der benutzerdefinierten Skripterweiterung ein.

```powershell
Net user <username> /active:yes
```

Mit dem folgenden PowerShell-Ausschnitt werden alle Administratorkonten als aktiv markiert, einschließlich des integrierten Administrators. Dieses Beispiel ist hilfreich, wenn der Benutzername des integrierten Administratorkontos unbekannt ist.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
Weitere Informationen finden Sie unter: 
* [Hochladen einer VHD in Azure](upload-generalized-managed.md)
* [Vorbereiten einer Windows-VHD für das Hochladen in Azure](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Bereitstellen von Windows 10 mit mehrinstanzenfähigen Hostingrechten
Stellen Sie sicher, dass Sie die [neueste Azure PowerShell-Version installiert und konfiguriert](/powershell/azure/)haben. Nachdem Sie die virtuelle Festplatte vorbereitet haben, laden Sie sie mit dem `Add-AzVhd`-Cmdlet wie folgt in Ihr Azure Storage-Konto hoch:

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Bereitstellen mithilfe der Azure Resource Manager-Vorlagenbereitstellung** In Ihren Resource Manager-Vorlagen kann ein zusätzlicher Parameter für `licenseType` angegeben werden. Informieren Sie sich weiter über das [Erstellen von Azure Resource Manager-Vorlagen](../../azure-resource-manager/templates/template-syntax.md). Nachdem Sie die virtuelle Festplatte in Azure hochgeladen haben, bearbeiten Sie die Resource Manager-Vorlage, um den Lizenztyp als Teil des Computeanbieters einzuschließen, und stellen die Vorlage als normale Vorlage bereit:
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**Bereitstellen über PowerShell** Beim Bereitstellen der Windows Server-VM über PowerShell ist ein zusätzlicher Parameter für `-LicenseType` vorhanden. Nachdem Sie die virtuelle Festplatte in Azure hochgeladen haben, erstellen Sie einen virtuellen Computer mit `New-AzVM` und geben den Lizenzierungstyp wie folgt an:
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Überprüfen, ob für den virtuellen Computer der Lizenzierungsvorteil genutzt wird
Sobald Sie den virtuellen Computer über die PowerShell- oder Resource Manager-Bereitstellungsmethode bereitgestellt haben, überprüfen Sie die den Lizenztyp wie folgt mit `Get-AzVM` :
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Die Ausgabe sieht in etwa wie im folgenden Beispiel für Windows 10 mit dem entsprechenden Lizenztyp aus:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Diese Ausgabe steht im Gegensatz zu den folgenden virtuellen Computern, die ohne Lizenzierung für den Azure-Vorteil bei Hybridnutzung bereitgestellt wurden, z.B. ein virtueller Computer, der direkt aus dem Azure-Katalog bereitgestellt wurde:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Weitere Informationen für den Beitritt zu Azure AD
Azure stellt alle Windows-VMs mit integriertem Administratorkonto bereit, das nicht für den Beitritt zu AAD verwendet werden kann. Beispielsweise funktioniert der Pfad *Einstellungen > Konto > Auf Arbeits- oder Schulkonto zugreifen > +Verbinden* nicht. Sie müssen ein zweites Administratorkonto erstellen und sich darüber anmelden, um manuell Azure AD beizutreten. Sie können Azure AD auch mithilfe eines Bereitstellungspakets konfigurieren. Weitere Informationen finden Sie unter dem Link im Abschnitt *Nächste Schritte*.

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über das [Konfigurieren von VDA für Windows 10](/windows/deployment/vda-subscription-activation).
- Erfahren Sie mehr über [Mehrinstanzenfähiges Hosting für Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).
