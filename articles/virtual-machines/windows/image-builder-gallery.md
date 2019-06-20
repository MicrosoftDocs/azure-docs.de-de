---
title: Verwenden Sie Azure Image Builder mit einem Imagekatalog für Windows-VMs (Vorschauversion)
description: Erstellen Sie Windows-Images mit Azure Image Builder und einem Katalog für freigegebene Images.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-widows
manager: jeconnoc
ms.openlocfilehash: 2453d37720bcf48b95b428cf78c6186de40b31aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65157940"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Vorschau: Erstellen eines Windows-Image und Verteilen in einem Katalog für freigegebene Images 

In diesem Artikel erfahren Sie, wie Sie Azure Image Builder verwenden können, um eine Imageversion in einem [Katalog für freigegebene Images](shared-image-galleries.md) zu erstellen und dann global zu verteilen.

Wir verwenden eine JSON-Vorlage, um das Image zu konfigurieren. Wir verwenden die JSON-Datei [helloImageTemplateforWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json). 

Die Vorlage verwendet [sharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) als Wert für den `distribute`-Abschnitt der Vorlage, um das Image in einem Katalog für freigegebene Images zu verteilen.

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
az provider show -n Microsoft.Compute | grep registrationState
```

Wenn nicht „registered“ ausgegeben wird, führen Sie den folgenden Befehl aus:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```

## <a name="set-variables-and-permissions"></a>Festlegen von Variablen und Berechtigungen 

Einige Angaben verwenden wir wiederholt. Aus diesem Grund erstellen wir einige Variablen, um diese Informationen zu speichern. Ersetzen Sie die Werte für die Variablen, wie `username` und `vmpassword`, durch Ihre eigenen Angaben.

```azurecli-interactive
# Resource group name - we are using ibsigRG in this example
sigResourceGroup=myIBWinRG
# Datacenter location - we are using West US 2 in this example
location=westus
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=my22stSIG
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=winSvrimages
# image distribution metadata reference name
runOutputName=w2019SigRo
# User name and password for the VM
username="azureuser"
vmpassword="passwordfortheVM"
```

Erstellen Sie eine Variable für Ihre Abonnement-ID. Diese können Sie mit `az account show | grep id` abrufen.

```azurecli-interactive
subscriptionID="Subscription ID"
```

Erstellen Sie die Ressourcengruppe.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Erteilen Sie Azure Image Builder die Berechtigung, Ressourcen in dieser Ressourcengruppe erstellen zu können. Der Wert `--assignee` ist die App-Registrierungs-ID für den Image Builder-Dienst. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>Erstellen einer Imagedefinition und eines Imagekatalogs

Erstellen Sie einen Imagekatalog. 

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Erstellen Sie eine Imagedefinition.

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher corpIT \
   --offer myOffer \
   --sku 2019 \
   --os-type Windows
```


## <a name="download-and-configure-the-json"></a>Herunterladen und Konfigurieren der JSON-Datei

Laden Sie die JSON-Vorlage herunter, und konfigurieren Sie diese mit Ihren Variablen.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json -o helloImageTemplateforWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforWinSIG.json
```

## <a name="create-the-image-version"></a>Erstellen der Imageversion

Im folgenden Teil erstellen Sie die Imageversion im Katalog. 

Senden Sie die Imagekonfiguration an den Azure Image Builder-Dienst.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
```

Starten Sie den Imagebuild.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforWinSIG01 \
     --action Run 
```

Das Erstellen des Images und Replizieren in beiden Regionen kann einige Zeit in Anspruch nehmen. Warten Sie den Abschluss des Vorgangs ab, bevor Sie eine VM erstellen.


## <a name="create-the-vm"></a>Erstellen des virtuellen Computers

Erstellen Sie eine VM aus der Imageversion, die von Azure Image Builder erstellt wurde.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm001 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```


## <a name="verify-the-customization"></a>Überprüfen der Anpassung
Stellen Sie eine Remotedesktopverbindung mit der VM mit dem Benutzernamen und dem Kennwort her, die Sie beim Erstellen der VM festgelegt haben. Öffnen Sie in der VM eine Eingabeaufforderung, und geben Sie Folgendes ein:

```console
dir c:\
```

Es sollte das Verzeichnis `buildActions` angezeigt werden, das bei der Anpassung des Images erstellt wurde.


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie die Imageversion erneut anpassen möchten, um eine neue Version desselben Images zu erstellen, **überspringen Sie den nächsten Schritt**, und [erstellen Sie mit Azure Image Builder eine weitere Imageversion](image-builder-gallery-update-image-version.md).


Das erstellte Image und dessen Ressourcendateien werden gelöscht. Achten Sie darauf, dass Sie mit dieser Bereitstellung fertig sind, bevor Sie die Ressourcen löschen.

Wenn Sie die Imagekatalogressourcen löschen, müssen Sie alle Imageversionen löschen, bevor Sie die Imagedefinition löschen können, die für deren Erstellung verwendet wurde. Sie müssen zunächst alle Imagedefinitionen im Katalog löschen, um den Katalog löschen zu können.

Löschen Sie die Image Builder-Vorlage.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
```

Rufen Sie die von Image Builder erstellte Imageversion ab (diese beginnt immer mit `0.`), und löschen Sie diese.

```azurecli-interactive
sigDefImgVersion=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'name' -o json | grep 0. | tr -d '"')
az sig image-version delete \
   -g $sigResourceGroup \
   --gallery-image-version $sigDefImgVersion \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```   


Löschen Sie die Imagedefinition.

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

Löschen Sie den Katalog.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

Löschen Sie die Ressourcengruppe.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Aktualisieren der Imageversion finden Sie im Artikel zum [Erstellen einer weiteren Imageversion mit Azure Image Builder](image-builder-gallery-update-image-version.md).