---
title: Erstellen eines VM-Images und Verwenden einer benutzerseitig zugewiesenen verwalteten Identität zum Zugreifen auf Dateien in Azure Storage (Vorschauversion)
description: Erstellen Sie ein VM-Image mit Azure Image Builder, das mithilfe einer benutzerseitig zugewiesenen verwalteten Identität auf in Azure Storage gespeicherte Dateien zugreifen kann.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: b0a6c016b2be12ac6686b3748b4b16281899323e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65511059"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Erstellen eines Images und Verwenden einer benutzerseitig zugewiesenen verwalteten Identität zum Zugreifen auf Dateien in Azure Storage 

Azure Image Builder unterstützt standardmäßig die Verwendung von Skripts und das Kopieren von Dateien aus mehreren Quellen wie z. B. GitHub und Azure Storage. Azure Image Builder muss extern auf diese zugreifen können, um sie verwenden zu können. Sie können jedoch Azure Storage-Blobs mit SAS-Token schützen.

In diesem Artikel erfahren Sie, wie Sie mit Azure Image Builder ein benutzerdefiniertes Image erstellen. Dabei verwendet der Dienst eine [benutzerseitig zugewiesene verwaltete Identität](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), um für die Imageanpassung auf Dateien in Azure Storage zuzugreifen, ohne die Dateien öffentlich verfügbar machen zu müssen, oder mithilfe von SAS-Token.

Im unten verwendeten Beispiel erstellen Sie zwei Ressourcengruppen. Eine wird für das benutzerdefinierte Image verwendet, und die andere hostet das Azure-Speicherkonto, das eine Skriptdatei enthält. Dadurch wird ein realitätsnahes Szenario simuliert, bei dem Sie Artefakte oder Imagedateien außerhalb von Azure Image Builder in unterschiedlichen Speicherkonten erstellt haben. Sie erstellen eine benutzerseitig zugewiesene Identität, der Sie dann Leseberechtigungen für die Skriptdatei gewähren, ohne die Datei öffentlich verfügbar zu machen. Anschließend verwenden Sie die Shellanpassung, um das Skript aus dem Speicherkonto herunterzuladen und auszuführen.


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
# Image resource group name 
imageResourceGroup=aibmdimsi
# storage resource group
strResourceGroup=aibmdimsistor
# Location 
location=WestUS2
# name of the image to be created
imageName=aibCustLinuxImgMsi01
# image distribution metadata reference name
runOutputName=u1804ManImgMsiro
```

Erstellen Sie eine Variable für Ihre Abonnement-ID. Diese können Sie mit `az account show | grep id` abrufen.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

Erstellen Sie die Ressourcengruppen für das Image und das Speichern des Skripts.

```azurecli-interactive
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```


Erstellen Sie den Speicher, kopieren Sie das Beispielskript von GitHub, und fügen Sie es dort ein.

```azurecli-interactive
# script storage account
scriptStorageAcc=aibstorscript$(date +'%s')

# script container
scriptStorageAccContainer=scriptscont$(date +'%s')

# script url
scriptUrl=https://$scriptStorageAcc.blob.core.windows.net/$scriptStorageAccContainer/customizeScript.sh

# create storage account and blob in resource group
az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS

az storage container create -n $scriptStorageAccContainer --fail-on-exist --account-name $scriptStorageAcc

# copy in an example script from the GitHub repo 
az storage blob copy start \
    --destination-blob customizeScript.sh \
    --destination-container $scriptStorageAccContainer \
    --account-name $scriptStorageAcc \
    --source-uri https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```



Erteilen Sie Azure Image Builder die Berechtigung, Ressourcen in dieser Imageressourcengruppe erstellen zu können. Der Wert `--assignee` ist die App-Registrierungs-ID für den Image Builder-Dienst. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="create-user-assigned-managed-identity"></a>Erstellen einer benutzerseitig zugewiesenen verwalteten Identität

Erstellen Sie die Identität, und gewähren Sie ihr Berechtigungen für das Skriptspeicherkonto. Weitere Informationen finden Sie unter „Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe der Azure-Befehlszeilenschnittstelle“ im Abschnitt [Benutzerseitig zugewiesene verwaltete Identität](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

```azurecli-interactive
# Create the user assigned identity 
identityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $identityName
# assign the identity permissions to the storage account, so it can read the script blob
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
# create the user identity URI
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$identityName
```


## <a name="modify-the-example"></a>Anpassen des Beispiels

Laden Sie die JSON-Beispieldatei herunter, und konfigurieren Sie sie mit den erstellten Variablen.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateMsi.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateMsi.json
sed -i -e "s/<region>/$location/g" helloImageTemplateMsi.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateMsi.json
sed -i -e "s%<scriptUrl>%$scriptUrl%g" helloImageTemplateMsi.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateMsi.json
sed -i -e "s%<runOutputName>%$runOutputName%g" helloImageTemplateMsi.json
```

## <a name="create-the-image"></a>Erstellen des Images

Senden Sie die Imagekonfiguration an den Azure Image Builder-Dienst.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

Starten Sie den Imagebuild.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

Warten Sie, bis der Buildvorgang abgeschlossen wird. Der Vorgang kann bis zu 15 Minuten dauern.

## <a name="create-a-vm"></a>Erstellen einer VM

Erstellen Sie anhand des Images eine VM. 

```bash
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Starten Sie eine SSH-Sitzung, nachdem Sie die VM erstellt haben.

```azurecli-interactive
ssh aibuser@<publicIp>
```

Dann sollten Sie sehen, dass das Image angepasst wurde. Es wird eine Nachricht des Tages ausgegeben, sobald die SSH-Verbindung hergestellt wurde.

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>Bereinigen

Wenn Sie fertig sind, können Sie die Ressourcen löschen, wenn Sie sie nicht mehr brauchen.

```azurecli-interactive
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

Sollten Sie Probleme bei der Arbeit mit Azure Image Builder haben, finden Sie im Artikel zur [Problembehandlung](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json) weitere Informationen.