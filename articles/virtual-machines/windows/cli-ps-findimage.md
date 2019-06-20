---
title: Auswählen von Windows-VM-Images in Azure | Microsoft-Dokumentation
description: Verwenden Sie Azure PowerShell, um den Herausgeber, das Angebot, die SKU und die Version für Marketplace-VM-Images zu ermitteln.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/25/2019
ms.author: cynthn
ms.openlocfilehash: 893b71d3a1cc6ece8272cb1a372302ff384003dd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64693767"
---
# <a name="find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Suchen nach Windows-VM-Images im Azure Marketplace mit Azure PowerShell

Dieser Artikel beschreibt, wie Sie mit Azure PowerShell nach VM-Images im Azure Marketplace suchen. Sie können dann ein Marketplace-Image angeben, wenn Sie einen virtuellen Computer programmgesteuert mit PowerShell, Resource Manager-Vorlagen oder anderen Tools erstellen.

Sie können auch über die [Azure Marketplace](https://azuremarketplace.microsoft.com/)-Storefront, das [Azure-Portal](https://portal.azure.com) oder die [Azure-Befehlszeilenschnittstelle](../linux/cli-ps-findimage.md) die verfügbaren Images und Angebote durchsuchen. 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="table-of-commonly-used-windows-images"></a>Tabelle mit häufig verwendeten Windows-Images

Die folgende Tabelle enthält eine Teilmenge der verfügbaren SKUs für die angegebenen Herausgeber und Angebote:

| Herausgeber | Angebot | Sku |
|:--- |:--- |:--- |
| MicrosoftWindowsServer |Windows Server |2019-Datacenter |
| MicrosoftWindowsServer |Windows Server |2019-Datacenter-Core |
| MicrosoftWindowsServer |Windows Server |2019-Datacenter-with-Containers |
| MicrosoftWindowsServer |Windows Server |2016-Datacenter |
| MicrosoftWindowsServer |Windows Server |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |Windows Server |2016-Datacenter-with-Containers |
| MicrosoftWindowsServer |Windows Server |2012-R2-Datacenter |
| MicrosoftWindowsServer |Windows Server |2012-Datacenter |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2019 |
| MicrosoftSQLServer |SQL2019-WS2016 |Enterprise |
| MicrosoftRServer |RServer-WS2016 |Enterprise |

## <a name="navigate-the-images"></a>Navigieren zu den Images

Eine Möglichkeit, ein Image an einem Speicherort zu suchen, stellt das aufeinanderfolgende Ausführen der Cmdlets [Get-AzVMImagePublisher](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagepublisher), [Get-AzVMImageOffer](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimageoffer) und [Get-AzVMImageSku](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagesku) dar:

1. Auflistung der Herausgeber von Images
2. Auflistung der Angebote eines bestimmten Anbieters
3. Auflistung der SKUs eines bestimmten Angebots

Führen Sie dann für eine ausgewählte SKU [Get-AzVMImage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimage) zum Auflisten der bereitzustellenden Versionen aus.

1. Listen Sie die Herausgeber auf:

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```

2. Tragen Sie den Namen des ausgewählten Herausgebers ein, und listen Sie die Angebote auf:

    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```

3. Tragen Sie den Namen des ausgewählten Angebots ein, und listen Sie die SKUs auf:

    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```

4. Tragen Sie den Namen der ausgewählten SKU ein, und rufen Sie die Imageversion ab:

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    
Aus der Ausgabe des `Get-AzVMImage`-Befehls können Sie ein Versionsimage zum Bereitstellen eines neuen virtuellen Computers auswählen.

Das folgende Beispiel zeigt die vollständige Folge von Befehlen und deren Ausgaben:

```powershell
$locName="West US"
Get-AzVMImagePublisher -Location $locName | Select PublisherName
```

Hier sehen Sie einen Teil der Ausgabe:

```
PublisherName
-------------
...
abiquo
accedian
accellion
accessdata-group
accops
Acronis
Acronis.Backup
actian-corp
actian_matrix
actifio
activeeon
adgs
advantech
advantech-webaccess
advantys
...
```

Für den Herausgeber von *MicrosoftWindowsServer*:

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
```

Ausgabe:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServerSemiAnnual
```

Für das Angebot *WindowsServer*:

```powershell
$offerName="WindowsServer"
Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
```

Hier sehen Sie einen Teil der Ausgabe:

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Datacenter-with-RDSH
2019-Datacenter
2019-Datacenter-Core
2019-Datacenter-Core-smalldisk
2019-Datacenter-Core-with-Containers
...
```

Dann für die SKU *2019-Datacenter*:

```powershell
$skuName="2019-Datacenter"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
```

Nun können Sie Herausgeber, Angebot, SKU und Version Ihrer Wahl in einem URN kombinieren (Trennung der Werte durch „:“). Übergeben Sie diesen URN mit dem Parameter `--image`, wenn Sie einen virtuellen Computer mit dem Cmdlet [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) erstellen. Sie können optional die Versionsnummer im URN durch „latest“ ersetzen, um die aktuelle Version des Images abzurufen.

Wenn Sie einen virtuellen Computer mithilfe einer Resource Manager-Vorlage bereitstellen, legen Sie die Imageparameter einzeln in den `imageReference`-Eigenschaften fest. Informationen finden Sie in der [Vorlagenreferenz](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Anzeigen von Planeigenschaften

Um die Kaufplaninformationen eines Images anzuzeigen, führen Sie das `Get-AzVMImage`-Cmdlet aus. Wenn die `PurchasePlan`-Eigenschaft in der Ausgabe nicht `null` ist, müssen Sie vor der programmgesteuerten Bereitstellung Bedingungen des Images akzeptieren.  

Für das Image *Windows Server 2016 Datacenter* gelten z.B. keine weiteren Bedingungen, daher lautet die `PurchasePlan`-Angabe `null`:

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

Ausgabe:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2019.0.20190115
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2019-Datacenter
Version          : 2019.0.20190115
FilterExpression :
Name             : 2019.0.20190115
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

Im Beispiel unten wird ein ähnlicher Befehl für das Image *Data Science Virtual Machine – Windows 2016* angezeigt. Dieses weist folgenden `PurchasePlan`-Eigenschaften auf: `name`, `product` und `publisher`. Einige Images weisen auch eine `promotion code`-Eigenschaft auf. Sehen Sie sich zum Bereitstellen dieses Images die folgenden Abschnitte an, um die Bedingungen zu akzeptieren und die programmgesteuerte Bereitstellung zu ermöglichen.

```powershell
Get-AzVMImage -Location "westus" -PublisherName "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

Ausgabe:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMImage/Offers/windows-data-science-vm/Skus/windows2016/Versions/19.01.14
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 19.01.14
FilterExpression :
Name             : 19.01.14
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : {
                     "publisher": "microsoft-ads",
                     "name": "windows2016",
                     "product": "windows-data-science-vm"
                   }
DataDiskImages   : []

```

### <a name="accept-the-terms"></a>Akzeptieren der Bedingungen

Verwenden Sie zum Anzeigen der Lizenzbedingungen das Cmdlet [Get-AzMarketplaceterms](https://docs.microsoft.com/powershell/module/az.marketplaceordering/get-azmarketplaceterms), und übergeben Sie die Kaufplanparameter. Die Ausgabe enthält einen Link zu den Bedingungen für das Marketplace-Image und zeigt an, ob Sie zuvor die Geschäftsbedingungen akzeptiert haben. Verwenden Sie in den Parameterwerten ausschließlich Kleinbuchstaben.

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Ausgabe:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DVM%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 1/25/2019 7:43:00 PM
```

Verwenden Sie das Cmdlet [Set-AzMarketplaceterms](https://docs.microsoft.com/powershell/module/az.marketplaceordering/set-azmarketplaceterms) zum Annehmen oder Ablehnen der Bedingungen. Sie müssen für das Image die Bedingungen nur einmal pro Abonnement akzeptieren. Verwenden Sie in den Parameterwerten ausschließlich Kleinbuchstaben. 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```

Ausgabe:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : XXXXXXK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBXXXXXX
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```

### <a name="deploy-using-purchase-plan-parameters"></a>Bereitstellen mithilfe von Kaufplanparametern

Nach dem Akzeptieren der Bedingungen für ein Image können Sie einen virtuellen Computer in diesem Abonnement bereitstellen. Verwenden Sie wie im folgenden Codeausschnitt gezeigt das Cmdlet [Set-AzVMPlan](https://docs.microsoft.com/powershell/module/az.compute/set-azvmplan), um die Marketplace-Planinformationen für das VM-Objekt festzulegen. Ein vollständiges Skript zum Erstellen von Netzwerkeinstellungen für den virtuellen Computer und Fertigstellen der Bereitstellung finden Sie unter [Virtueller Azure-Computer – PowerShell-Beispiele](powershell-samples.md).

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace plan information

$publisherName = "microsoft-ads"

$productName = "windows-data-science-vm"

$planName = "windows2016"

$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

$cred=Get-Credential

$vmConfig = Set-AzVMOperatingSystem -Windows -VM $vmConfig -ComputerName "myVM" -Credential $cred

# Set the Marketplace image

$offerName = "windows-data-science-vm"

$skuName = "windows2016"

$version = "19.01.14"

$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version
...
```
Anschließend übergeben Sie die VM-Konfiguration zusammen mit Netzwerkkonfigurationsobjekten an das Cmdlet `New-AzVM`.

## <a name="next-steps"></a>Nächste Schritte

Um mit den grundlegenden Imageinformationen schnell mit dem Cmdlet `New-AzVM` einen virtuellen Computer zu erstellen, lesen Sie [Erstellen eines virtuellen Windows-Computers mit PowerShell](quick-create-powershell.md).


Ein PowerShell-Skriptbeispiel finden Sie unter [Erstellen einer vollständig konfigurierten VM](../scripts/virtual-machines-windows-powershell-sample-create-vm.md).


