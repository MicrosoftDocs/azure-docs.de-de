---
title: Erstellen einer Windows-VM mit Azure Image Builder unter Verwendung von PowerShell
description: Erstellen Sie eine Windows-VM mit dem PowerShell-Modul von Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 06/17/2020
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7e902798284240b55a3b08ea55ab6ee55add2431
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575837"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder-using-powershell"></a>Vorschau: Erstellen einer Windows-VM mit Azure Image Builder unter Verwendung von PowerShell

In diesem Artikel erfahren Sie, wie Sie mit dem PowerShell-Modul von Azure VM Image Builder ein benutzerdefiniertes Windows-Image erstellen können.

> [!CAUTION]
> Azure Image Builder ist derzeit als öffentliche Vorschauversion verfügbar. Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt. Für Produktionsworkloads wird sie nicht empfohlen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

Falls Sie PowerShell lokal verwenden möchten, müssen Sie für diesen Artikel das Az PowerShell-Modul installieren und mit dem Cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) eine Verbindung mit Ihrem Azure-Konto herstellen. Weitere Informationen zum Installieren des Az PowerShell-Moduls finden Sie unter [Installieren von Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Solange sich die PowerShell-Module von **Az.ImageBuilder** und **Az.ManagedServiceIdentity** noch in der Vorschauphase befinden, müssen Sie sie separat mithilfe des Cmdlets `Install-Module` mit dem Parameter `AllowPrerelease` installieren. Sobald diese PowerShell-Module allgemein verfügbar sind, werden sie in künftige Releases des Az PowerShell-Moduls integriert und in Azure Cloud Shell nativ zur Verfügung gestellt.

```azurepowershell-interactive
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}
```

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Wenn Sie über mehrere Azure-Abonnements verfügen, müssen Sie das entsprechende Abonnement auswählen, in dem die Ressourcen fakturiert werden sollen. Wählen Sie mit dem Cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) ein bestimmtes Abonnement aus.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

### <a name="register-features"></a>Registrieren von Funktionen

Wenn Sie Azure Image Builder zum ersten Mal während der Vorschau verwenden, registrieren Sie das neue Feature **VirtualMachineTemplatePreview**.

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

Überprüfen Sie den Status der Featureregistrierung.

> [!NOTE]
> **RegistrationState** kann einige Minuten lang den Status `Registering` aufweisen, bevor der Wechsel in `Registered` erfolgt. Warten Sie, bis der Status **Registriert** lautet, bevor Sie fortfahren.

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

Registrieren Sie die folgenden Ressourcenanbieter für die Verwendung mit dem Azure-Abonnement, wenn diese Anbieter noch nicht registriert sind.

- Microsoft.Compute
- Microsoft.KeyVault
- Microsoft.Storage
- Microsoft.VirtualMachineImages

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute, Microsoft.KeyVault, Microsoft.Storage, Microsoft.VirtualMachineImages |
  Where-Object RegistrationState -ne Registered |
    Register-AzResourceProvider
```

## <a name="define-variables"></a>Definieren von Variablen

Sie verwenden mehrere Informationselemente wiederholt. Erstellen Sie Variablen zum Speichern der Informationen.

```azurepowershell-interactive
# Destination image resource group name
$imageResourceGroup = 'myWinImgBuilderRG'

# Azure region
$location = 'WestUS2'

# Name of the image to be created
$imageTemplateName = 'myWinImage'

# Distribution properties of the managed image upon completion
$runOutputName = 'myDistResults'
```

Erstellen Sie eine Variable für Ihre Azure Abonnement-ID. Um sich zu vergewissern, dass die `subscriptionID`-Variable Ihre Abonnement-ID enthält, können Sie die zweite Zeile im folgenden Beispiel ausführen.

```azurepowershell-interactive
# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id
Write-Output $subscriptionID
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine [Azure-Ressourcengruppe](../../azure-resource-manager/management/overview.md). Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen als Gruppe bereitgestellt und verwaltet werden.

Im folgenden Beispiel wird eine Ressourcengruppe basierend auf dem Namen in der Variablen `$imageResourceGroup` in der Region erstellt, die in der Variablen `$location` angegeben ist. Diese Ressourcengruppe wird verwendet, um das Artefakt und Image der Imagekonfigurationsvorlage zu speichern.

```azurepowershell-interactive
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="create-user-identity-and-set-role-permissions"></a>Erstellen der Benutzeridentität und Festlegen von Rollenberechtigungen

Erteilen Sie mithilfe des folgenden Beispiels Azure Image Builder-Berechtigungen für das Erstellen von Images in der angegebenen Ressourcengruppe. Ohne diese Berechtigung kann der Builderstellungsprozess nicht erfolgreich abgeschlossen werden.

Erstellen Sie Variablen für die Rollendefinition und die Identitätsnamen. Diese Werte müssen eindeutig sein.

```azurepowershell-interactive
[int]$timeInt = $(Get-Date -UFormat '%s')
$imageRoleDefName = "Azure Image Builder Image Def $timeInt"
$identityName = "myIdentity$timeInt"
```

Erstellen Sie eine Benutzeridentität.

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName
```

Speichern Sie die Identitätsressource und die Prinzipal-IDs in Variablen.

```azurepowershell-interactive
$identityNameResourceId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```

### <a name="assign-permissions-for-identity-to-distribute-images"></a>Zuweisen von Berechtigungen für die Identität zum Verteilen von Images

Laden Sie die JSON-Konfigurationsdatei herunter, und ändern Sie die Datei basierend auf den in diesem Artikel definierten Einstellungen.

```azurepowershell-interactive
$myRoleImageCreationUrl = 'https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json'
$myRoleImageCreationPath = "$env:TEMP\myRoleImageCreation.json"

Invoke-WebRequest -Uri $myRoleImageCreationUrl -OutFile $myRoleImageCreationPath -UseBasicParsing

$Content = Get-Content -Path $myRoleImageCreationPath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $imageResourceGroup
$Content = $Content -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName
$Content | Out-File -FilePath $myRoleImageCreationPath -Force
```

Erstellen Sie die Rollendefinition.

```azurepowershell-interactive
New-AzRoleDefinition -InputFile $myRoleImageCreationPath
```

Weisen Sie die Rollendefinition dem Image Builder-Dienstprinzipal zu.

```azurepowershell-interactive
$RoleAssignParams = @{
  ObjectId = $identityNamePrincipalId
  RoleDefinitionName = $imageRoleDefName
  Scope = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
}
New-AzRoleAssignment @RoleAssignParams
```

> [!NOTE]
> Bei Anzeige der Fehlermeldung „_New-AzRoleDefinition: Das Limit für Rollendefinitionen ist überschritten. Es können keine weiteren Rollendefinitionen erstellt werden._ “, beachten Sie die Informationen unter [Problembehandlung für Azure RBAC](../../role-based-access-control/troubleshooting.md).

## <a name="create-a-shared-image-gallery"></a>Erstellen einer Shared Image Gallery-Instanz

Erstellen Sie den Katalog.

```azurepowershell-interactive
$myGalleryName = 'myImageGallery'
$imageDefName = 'winSvrImages'

New-AzGallery -GalleryName $myGalleryName -ResourceGroupName $imageResourceGroup -Location $location
```

Erstellen Sie eine Katalogdefinition.

```azurepowershell-interactive
$GalleryParams = @{
  GalleryName = $myGalleryName
  ResourceGroupName = $imageResourceGroup
  Location = $location
  Name = $imageDefName
  OsState = 'generalized'
  OsType = 'Windows'
  Publisher = 'myCo'
  Offer = 'Windows'
  Sku = 'Win2019'
}
New-AzGalleryImageDefinition @GalleryParams
```

## <a name="create-an-image"></a>Erstellen eines Images

Erstellen Sie ein Azure Image Builder-Quellobjekt. Gültige Parameterwerte finden Sie unter [Suchen nach Windows-VM-Images im Azure Marketplace mit Azure PowerShell](./cli-ps-findimage.md).

```azurepowershell-interactive
$SrcObjParams = @{
  SourceTypePlatformImage = $true
  Publisher = 'MicrosoftWindowsServer'
  Offer = 'WindowsServer'
  Sku = '2019-Datacenter'
  Version = 'latest'
}
$srcPlatform = New-AzImageBuilderSourceObject @SrcObjParams
```

Erstellen Sie ein Azure Image Builder-Distributor-Objekt.

```azurepowershell-interactive
$disObjParams = @{
  SharedImageDistributor = $true
  ArtifactTag = @{tag='dis-share'}
  GalleryImageId = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup/providers/Microsoft.Compute/galleries/$myGalleryName/images/$imageDefName"
  ReplicationRegion = $location
  RunOutputName = $runOutputName
  ExcludeFromLatest = $false
}
$disSharedImg = New-AzImageBuilderDistributorObject @disObjParams
```

Erstellen Sie ein Azure Image Builder-Anpassungsobjekt.

```azurepowershell-interactive
$ImgCustomParams01 = @{
  PowerShellCustomizer = $true
  CustomizerName = 'settingUpMgmtAgtPath'
  RunElevated = $false
  Inline = @("mkdir c:\\buildActions", "mkdir c:\\buildArtifacts", "echo Azure-Image-Builder-Was-Here  > c:\\buildActions\\buildActionsOutput.txt")
}
$Customizer01 = New-AzImageBuilderCustomizerObject @ImgCustomParams01
```

Erstellen Sie ein zweites Azure Image Builder-Anpassungsobjekt.

```azurepowershell-interactive
$ImgCustomParams02 = @{
  FileCustomizer = $true
  CustomizerName = 'downloadBuildArtifacts'
  Destination = 'c:\\buildArtifacts\\index.html'
  SourceUri = 'https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html'
}
$Customizer02 = New-AzImageBuilderCustomizerObject @ImgCustomParams02
```

Erstellen Sie eine Azure Image Builder-Vorlage.

```azurepowershell-interactive
$ImgTemplateParams = @{
  ImageTemplateName = $imageTemplateName
  ResourceGroupName = $imageResourceGroup
  Source = $srcPlatform
  Distribute = $disSharedImg
  Customize = $Customizer01, $Customizer02
  Location = $location
  UserAssignedIdentityId = $identityNameResourceId
}
New-AzImageBuilderTemplate @ImgTemplateParams
```

Nach Abschluss des Vorgangs wird eine Meldung zurückgegeben, und in der `$imageResourceGroup` wird eine Konfigurationsvorlage für Image Builder erstellt.

Um zu ermitteln, ob die Vorlagenerstellung erfolgreich war, können Sie das folgende Beispiel verwenden.

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup |
  Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
```

Außerdem erstellt Image Builder im Hintergrund eine Stagingressourcengruppe in Ihrem Abonnement. Diese Ressourcengruppe wird für den Imagebuild verwendet. Sie hat das Format `IT_<DestinationResourceGroup>_<TemplateName>`.

> [!WARNING]
> Löschen Sie die Stagingressourcengruppe nicht direkt. Löschen Sie das Artefakt der Imagevorlage. Dabei wird die Stagingressourcengruppe automatisch gelöscht.

Gehen Sie folgendermaßen vor, wenn der Dienst während der Übermittlung der Imagekonfigurationsvorlage einen Fehler meldet:

- Weitere Informationen finden Sie unter [Problembehandlung im Zusammenhang mit AIB-Fehlern (Azure VM Image Builder)](../linux/image-builder-troubleshoot.md).
- Löschen Sie die Vorlage mithilfe des folgenden Beispiels, bevor Sie den Vorgang wiederholen.

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup
```

## <a name="start-the-image-build"></a>Starten des Imagebuilds

Senden Sie die Imagekonfiguration an den VM-Image Builder-Dienst.

```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

Warten Sie, bis der Image-Erstellungsprozess beendet ist. Dieser Schritt kann bis zu einer Stunde dauern.

Wenn Fehler auftreten, beachten Sie das Dokument [Problembehandlung bei AIB-Fehlern (Azure VM Image Build)](../linux/image-builder-troubleshoot.md).

## <a name="create-a-vm"></a>Erstellen einer VM

Speichern Sie die Anmeldeinformationen für die VM in einer Variablen. Das Kennwort muss komplex sein.

```azurepowershell-interactive
$Cred = Get-Credential
```

Erstellen Sie die VM mithilfe des erstellten Images.

```azurepowershell-interactive
$ArtifactId = (Get-AzImageBuilderRunOutput -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup).ArtifactId

New-AzVM -ResourceGroupName $imageResourceGroup -Image $ArtifactId -Name myWinVM01 -Credential $Cred
```

## <a name="verify-the-customizations"></a>Überprüfen der Anpassungen

Stellen Sie eine Remotedesktopverbindung mit der VM mit dem Benutzernamen und dem Kennwort her, die Sie beim Erstellen der VM festgelegt haben. Öffnen Sie in der VM PowerShell, und führen Sie `Get-Content` wie im folgenden Beispiel gezeigt aus:

```azurepowershell-interactive
Get-Content -Path C:\buildActions\buildActionsOutput.txt
```

Die Ausgabe sollte auf dem Inhalt der Datei basieren, die während der Image-Anpassung erstellt wurde.

```Output
Azure-Image-Builder-Was-Here
```

Überprüfen Sie in derselben PowerShell-Sitzung, ob die zweite Anpassung erfolgreich abgeschlossen wurde, indem Sie wie im folgenden Beispiel auf das Vorhandensein der Datei `c:\buildArtifacts\index.html` prüfen:

```azurepowershell-interactive
Get-ChildItem c:\buildArtifacts\
```

Das Ergebnis sollte eine Verzeichnisliste sein, die die während der Imageanpassung heruntergeladene Datei anzeigt.

```Output
    Directory: C:\buildArtifacts

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---          29/01/2021    10:04            276 index.html
```


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die in diesem Artikel erstellten Ressourcen nicht benötigt werden, können Sie sie löschen, indem Sie die folgenden Beispiele ausführen.

### <a name="delete-the-image-builder-template"></a>Löschen der Image Builder-Vorlage

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

### <a name="delete-the-image-resource-group"></a>Löschen der Imageressourcengruppe

> [!CAUTION]
> Im folgenden Beispiel werden die angegebene Ressourcengruppe und alle darin enthaltenen Ressourcen gelöscht.
> Falls in der angegebenen Ressourcengruppe Ressourcen enthalten sind, die nicht zum Umfang dieses Artikels gehören, werden sie ebenfalls gelöscht.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $imageResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Komponenten der in diesem Artikel verwendeten JSON-Datei finden Sie unter [Image Builder-Vorlagenreferenz](../linux/image-builder-json.md).
