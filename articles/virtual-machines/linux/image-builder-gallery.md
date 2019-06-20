---
title: Verwenden von Azure Image Builder mit einem Imagekatalog für virtuelle Linux-Computer (Vorschauversion)
description: Erstellen Sie Linux-Images mit Azure Image Builder und einem Katalog für freigegebene Images.
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: d29fa8700cb1f530cfe85f0bdf6852d75ec1613e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65508166"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>Vorschau: Erstellen eines Linux-Images und Verteilen des Images über einen Katalog für freigegebene Images 

In diesem Artikel erfahren Sie, wie Sie mit Azure Image Builder eine Imageversion in einem [Katalog für freigegebene Images](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries) erstellen und wie Sie diese dann global verteilen.


Wir verwenden zum Konfigurieren des Images eine JSON-Beispielvorlage. Wir verwenden die JSON-Datei [helloImageTemplateforSIG.json](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

Die Vorlage verwendet [sharedImage](image-builder-json.md#distribute-sharedimage) als Wert für den `distribute`-Abschnitt der Vorlage, um das Image in einem Katalog für freigegebene Images zu verteilen.

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

## <a name="set-variables-and-permissions"></a>Festlegen von Variablen und Berechtigungen 

Einige Angaben verwenden wir wiederholt. Aus diesem Grund erstellen wir einige Variablen, um diese Informationen zu speichern.

In der Vorschauversion unterstützt Image Builder nur das Erstellen von benutzerdefinierten Images in derselben Ressourcengruppe, in der sich auch das verwaltete Quellimage befindet. Aktualisieren Sie den Namen der Ressourcengruppe in diesem Beispiel, sodass es sich um dieselbe Ressourcengruppe handelt, in der sich auch das verwaltete Quellimage befindet.

```azurecli-interactive
# Resource group name - we are using ibLinuxGalleryRG in this example
sigResourceGroup=ibLinuxGalleryRG
# Datacenter location - we are using West US 2 in this example
location=westus2
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=myIbGallery
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibLinuxSIG
```

Erstellen Sie eine Variable für Ihre Abonnement-ID. Diese können Sie mit `az account show | grep id` abrufen.

```azurecli-interactive
subscriptionID=<Subscription ID>
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
   --publisher myIbPublisher \
   --offer myOffer \
   --sku 18.04-LTS \
   --os-type Linux
```


## <a name="download-and-configure-the-json"></a>Herunterladen und Konfigurieren der JSON-Datei

Laden Sie die JSON-Vorlage herunter, und konfigurieren Sie diese mit Ihren Variablen.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>Erstellen der Imageversion

Im folgenden Teil erstellen Sie die Imageversion im Katalog. 

Senden Sie die Imagekonfiguration an den Azure Image Builder-Dienst.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Starten Sie den Imagebuild.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

Das Erstellen des Images und Replizieren in beiden Regionen kann einige Zeit in Anspruch nehmen. Warten Sie den Abschluss des Vorgangs ab, bevor Sie eine VM erstellen.


## <a name="create-the-vm"></a>Erstellen des virtuellen Computers

Erstellen Sie eine VM aus der Imageversion, die von Azure Image Builder erstellt wurde.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Stellen Sie eine SSH-Verbindung mit der VM her.

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

Sie sollten sehen, dass das Image angepasst wurde. Es wird eine *Nachricht des Tages* ausgegeben, sobald die SSH-Verbindung hergestellt wurde.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Imageversion erneut anpassen möchten, um eine neue Version desselben Images zu erstellen, überspringen Sie die nächsten Schritte, und [erstellen Sie mit Azure Image Builder eine weitere Imageversion](image-builder-gallery-update-image-version.md).


Das erstellte Image und dessen Ressourcendateien werden gelöscht. Achten Sie darauf, dass Sie mit dieser Bereitstellung fertig sind, bevor Sie die Ressourcen löschen.

Wenn Sie die Imagekatalogressourcen löschen, müssen Sie alle Imageversionen löschen, bevor Sie die Imagedefinition löschen können, die für deren Erstellung verwendet wurde. Sie müssen zunächst alle Imagedefinitionen im Katalog löschen, um den Katalog löschen zu können.

Löschen Sie die Image Builder-Vorlage.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
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

Erfahren Sie mehr über [Azure-Kataloge mit freigegebenen Images](shared-image-galleries.md).