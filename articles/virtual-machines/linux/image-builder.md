---
title: Erstellen eines virtuellen Linux-Computers mit Azure Image Builder (Vorschauversion)
description: Erstellen Sie einen virtuellen Linux-Computer mit Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: 854645af95d780053d94668921e41ac189bbbfb7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65157872"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Vorschau: Erstellen eines virtuellen Linux-Computers mit Azure Image Builder

In diesem Artikel erfahren Sie, wie Sie ein benutzerdefiniertes Linux-Image mit Azure Image Builder und der Azure-Befehlszeilenschnittstelle erstellen. Im Beispiel in diesem Artikel werden drei verschiedene [Anpassungen](image-builder-json.md#properties-customize) für das Image verwendet:

- Shell (ScriptUri): lädt ein [Shellskript](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh) herunter und führt es aus.
- Shell (inline): führt spezifische Befehle aus. In diesem Beispiel schließen die Inline-Befehle das Erstellen eines Verzeichnisses und das Aktualisieren des Betriebssystems ein.
- Datei: kopiert eine [Datei von GitHub](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) in ein Verzeichnis auf dem virtuellen Computer.

Wir verwenden zum Konfigurieren des Images eine JSON-Beispielvorlage. Wir verwenden die JSON-Datei [helloImageTemplateLinux.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

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
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
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
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

## <a name="create-the-image"></a>Erstellen des Images
Senden der Imagekonfiguration an den VM-Image Builder-Dienst

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Starten Sie den Imagebuild.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Warten Sie, bis der Build abgeschlossen ist. Der Vorgang kann bis zu 15 Minuten dauern.


## <a name="create-the-vm"></a>Erstellen des virtuellen Computers

Erstellen Sie den virtuellen Computer mithilfe des erstellten Images.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Ermitteln Sie die IP-Adresse aus der Ausgabe der VM-Erstellung, und stellen Sie damit eine SSH-Verbindung mit dem virtuellen Computer her.

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

Geben Sie zum Abschluss `exit` ein, um die SSH-Verbindung zu schließen.

## <a name="check-the-source"></a>Überprüfen der Quelle

In der Image Builder-Vorlage werden unter „Properties“ das Quellimage, das ausgeführte Anpassungsskript und der Ort der Verteilung angegeben.

```azurecli-interactive
cat helloImageTemplateLinux.json
```

Weitere ausführliche Informationen zu dieser JSON-Datei finden Sie unter [Image Builder-Vorlagenreferenz](image-builder-json.md).

## <a name="clean-up"></a>Bereinigen

Löschen Sie nach Abschluss des Vorgangs die Ressourcen.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01

az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Komponenten der in diesem Artikel verwendeten JSON-Datei finden Sie unter [Image Builder-Vorlagenreferenz](image-builder-json.md).