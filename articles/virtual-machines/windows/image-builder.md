---
title: Erstellen eines virtuellen Windows-Computers mit Azure Image Builder (Vorschauversion)
description: Erstellen Sie mit Azure Image Builder einen virtuellen Windows-Computer.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: jeconnoc
ms.openlocfilehash: 01109aa83c12bda9b1d21ec25784d663f8abf700
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65157900"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Vorschau: Erstellen eines virtuellen Windows-Computers mit Azure Image Builder

In diesem Artikel erfahren Sie, wie Sie mit Azure VM Image Builder ein benutzerdefiniertes Windows-Image erstellen können. Im Beispiel in diesem Artikel werden drei verschiedene [Anpassungen](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) für das Image verwendet:
- PowerShell (ScriptUri): lädt ein [PowerShell-Skript](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1) herunter und führt es aus.
- Windows-Neustart: startet die VM neu.
- PowerShell (inline): führt einen spezifischen Befehl aus. In diesem Beispiel wird mit `mkdir c:\\buildActions` ein Verzeichnis auf dem virtuellen Computer erstellt.
- Datei: kopiert eine Datei aus GitHub auf den virtuellen Computer. In diesem Beispiel wird [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) in `c:\buildArtifacts\index.html` auf den virtuellen Computer kopiert.

Wir verwenden zum Konfigurieren des Images eine JSON-Beispielvorlage. Wir verwenden die JSON-Datei [helloImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


> [!IMPORTANT]
> Azure Image Builder ist derzeit als öffentliche Vorschauversion (Public Preview) verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="register-the-features"></a>Registrieren des Features

Sie müssen das neue Feature registrieren, um Azure Image Builder während der Vorschauphase verwenden zu können.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Überprüfen Sie den Status der Featureregistrierung.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Überprüfen Sie die Registrierung.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Wenn nicht „registered“ ausgegeben wird, führen Sie den folgenden Befehl aus:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Einige Angaben verwenden wir wiederholt. Aus diesem Grund erstellen wir einige Variablen, um diese Informationen zu speichern.

```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myWinImgBuilderRG
# Region location 
location=WestUS2
# Name for the image 
imageName=myWinBuilderImage
# Run output name
runOutputName=aibWindows
# name of the image to be created
imageName=aibWinImage
```

Erstellen Sie eine Variable für Ihre Abonnement-ID. Diese können Sie mit `az account show | grep id` abrufen.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

Erstellen Sie die Ressourcengruppe.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

Erteilen Sie Image Builder die Berechtigung zum Erstellen von Ressourcen in dieser Ressourcengruppe. Der Wert `--assignee` ist die App-Registrierungs-ID für den Image Builder-Dienst. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-json-example"></a>Herunterladen des JSON-Beispiels

Laden Sie die JSON-Beispieldatei herunter, und konfigurieren Sie sie mit den erstellten Variablen.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

## <a name="create-the-image"></a>Erstellen des Images

Senden der Imagekonfiguration an den VM-Image Builder-Dienst

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Starten Sie den Imagebuild.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Warten Sie, bis der Build abgeschlossen ist. Der Vorgang kann bis zu 15 Minuten dauern.

## <a name="create-the-vm"></a>Erstellen des virtuellen Computers

Erstellen Sie den virtuellen Computer mithilfe des erstellten Images. Ersetzen Sie *<password>* durch Ihr eigenes Kennwort für `aibuser` auf dem virtuellen Computer.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>Überprüfen der Anpassung

Stellen Sie eine Remotedesktopverbindung mit der VM mit dem Benutzernamen und dem Kennwort her, die Sie beim Erstellen der VM festgelegt haben. Öffnen Sie in der VM eine Eingabeaufforderung, und geben Sie Folgendes ein:

```console
dir c:\
```

Bei der Anpassung des Images sollten diese beiden Verzeichnisse erstellt werden:
- buildActions
- buildArtifacts

## <a name="clean-up"></a>Bereinigen

Löschen Sie nach Abschluss des Vorgangs die Ressourcen.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
az group delete -n $imageResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Komponenten der in diesem Artikel verwendeten JSON-Datei finden Sie unter [Image Builder-Vorlagenreferenz](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

