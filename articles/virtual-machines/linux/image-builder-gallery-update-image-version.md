---
title: Erstellen einer neuen Imageversion aus einer vorhandenen Imageversion mit Azure Image Builder (Vorschauversion)
description: Erstellen Sie mit Azure Image Builder eine neue Imageversion aus einer vorhandenen Imageversion.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: 31ef53abcf9b416500ee70e42cc3cbd12cb11f35
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65157876"
---
# <a name="preview-create-a-new-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Vorschau: Erstellen einer neuen Imageversion aus einer vorhandenen Imageversion mit Azure Image Builder

In diesem Artikel erfahren Sie, wie Sie eine vorhandene Imageversion in einem [Katalog mit freigegebenen Images](shared-image-galleries.md) aktualisieren und als neue Imageversion im Katalog veröffentlichen.

Wir verwenden zum Konfigurieren des Images eine JSON-Beispielvorlage. Wir verwenden die JSON-Datei [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json). 


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


## <a name="set-variables-and-permissions"></a>Festlegen von Variablen und Berechtigungen

Wenn Sie Ihren Katalog mit freigegebenen Images anhand von [Erstellen eines ein Images und Verteilen des Images in einem Katalog mit freigegebenen Images](image-builder-gallery.md) erstellt haben, haben Sie bereits einige der benötigten Variablen erstellt. Richten Sie andernfalls nun einige Variablen ein, die in diesem Beispiel verwendet werden.

In der Vorschauversion unterstützt Image Builder nur das Erstellen von benutzerdefinierten Images in derselben Ressourcengruppe, in der sich auch das verwaltete Quellimage befindet. Aktualisieren Sie den Namen der Ressourcengruppe in diesem Beispiel, sodass es sich um dieselbe Ressourcengruppe handelt, in der sich auch das verwaltete Quellimage befindet.


```azurecli-interactive
# Resource group name 
sigResourceGroup=ibLinuxGalleryRG
# Gallery location 
location=westus2
# Additional region to replicate the image version to 
additionalregion=eastus
# Name of the shared image gallery 
sigName=myIbGallery
# Name of the image definition to use
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibSIGLinuxUpdate
```

Erstellen Sie eine Variable für Ihre Abonnement-ID. Diese können Sie mit `az account show | grep id` abrufen.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Rufen Sie die Imageversion ab, die Sie aktualisieren möchten.

```
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```


Wenn Sie bereits über einen eigenen Katalog mit freigegebenen Images verfügen und nicht nach dem vorherigen Beispiel vorgegangen sind, müssen Sie Image Builder Berechtigungen zum Zugreifen auf die Ressourcengruppe zuweisen, damit er auf den Katalog zugreifen kann.


```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="modify-helloimage-example"></a>Ändern des Beispiels helloImage
Sie können das verwendete Beispiel überprüfen, indem Sie die JSON-Datei [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) zusammen mit der [Image Builder-Vorlagenreferenz](image-builder-json.md) öffnen. 


Laden Sie das JSON-Beispiel herunter, und konfigurieren Sie es mit Ihren Variablen. 

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json -o helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromSIG.json
```

## <a name="create-the-image"></a>Erstellen des Images

Senden Sie die Imagekonfiguration an den VM-Image Builder-Dienst.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

Starten Sie den Imagebuild.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

Warten Sie, bis das Image erstellt und repliziert wurde, bevor Sie mit dem nächsten Schritt fortfahren.


## <a name="create-the-vm"></a>Erstellen des virtuellen Computers

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgVm001 \
  --admin-username azureuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Erstellen Sie eine SSH-Verbindung mit dem virtuellen Computer unter Verwendung seiner öffentlichen IP-Adresse.

```azurecli-interactive
ssh azureuser@<pubIp>
```

Sie sollten sehen, dass das Image angepasst wurde. Es wird eine „Nachricht des Tages“ ausgegeben, sobald die SSH-Verbindung hergestellt wurde.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Geben Sie `exit` ein, um die SSH-Verbindung zu schließen.

Sie können auch die Imageversionen auflisten, die nun in Ihrem Katalog verfügbar sind.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Komponenten der in diesem Artikel verwendeten JSON-Datei finden Sie unter [Image Builder-Vorlagenreferenz](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).