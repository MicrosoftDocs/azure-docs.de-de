---
title: Verwenden von Azure Image Builder für Linux-VMs mit Zugriff auf ein vorhandenes Azure-VNET (Vorschau)
description: Erstellen Sie Linux-VM-Images mit Azure Image Builder, um den Zugriff auf ein vorhandenes Azure-VNET zu ermöglichen.
author: danielsollondon
ms.author: danis
ms.date: 08/10/2020
ms.topic: how-to
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.reviewer: danis
ms.openlocfilehash: cbff2358dcf8685298e31f18c430c35ec7de4948
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96437336"
---
# <a name="use-azure-image-builder-for-linux-vms-allowing-access-to-an-existing-azure-vnet"></a>Verwenden von Azure Image Builder für Linux-VMs mit Zugriff auf ein vorhandenes Azure-VNET

In diesem Artikel erfahren Sie, wie Sie mithilfe von Azure Image Builder ein grundlegendes, angepasstes Linux-Image erstellen, das Zugriff auf vorhandene Ressourcen in einem VNET hat. Die von Ihnen erstellte Build-VM wird für ein neues oder vorhandenes VNET bereitgestellt, das Sie in Ihrem Abonnement angeben. Wenn Sie ein vorhandenes Azure-VNET verwenden, ist für den Azure VM Image Builder-Dienst keine Verbindung mit einem öffentlichen Netzwerk erforderlich.

> [!IMPORTANT]
> Azure Image Builder ist derzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="register-the-features"></a>Registrieren des Features

Sie müssen sich zunächst für den Azure VM Image Builder-Dienst registrieren. Die Registrierung gewährt Ihnen die Dienstberechtigungen zum Erstellen, Verwalten und Löschen einer Ressourcengruppe für das Staging. Der Dienst verfügt auch über die Berechtigung, der Gruppe Ressourcen hinzuzufügen, die für das Erstellen des Images erforderlich sind.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## <a name="set-variables-and-permissions"></a>Festlegen von Variablen und Berechtigungen 

Einige Informationselemente werden wiederholt verwendet. Erstellen Sie Variablen zum Speichern dieser Informationen.

```azurecli-interactive
# set your environment variables here!!!!

# destination image resource group
imageResourceGroup=aibImageRG01

# location (see possible locations in main docs)
location=WestUS2

# your subscription
# get the current subID : 'az account show | grep id'
subscriptionID=$(az account show | grep id | tr -d '",' | cut -c7-)

# name of the image to be created
imageName=aibCustomLinuxImg01

# image distribution metadata reference name
runOutputName=aibCustLinManImg01ro


# VNET properties (update to match your existing VNET, or leave as-is for demo)
# VNET name
vnetName=myexistingvnet01
# subnet name
subnetName=subnet01
# VNET resource group name
# NOTE! The VNET must always be in the same region as the AIB service region.
vnetRgName=existingVnetRG
# Existing Subnet NSG Name or the demo will create it
nsgName=aibdemoNsg
```

Erstellen Sie die Ressourcengruppe.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="configure-networking"></a>Konfigurieren der Netzwerkeinstellungen

Wenn Sie nicht über ein vorhandenes VNET, Subnetz oder eine Netzwerksicherheitsgruppe verfügen, erstellen Sie diese mithilfe des folgenden Skripts.

```bash

# Create a resource group

az group create -n $vnetRgName -l $location

# Create VNET

az network vnet create \
    --resource-group $vnetRgName \
    --name $vnetName --address-prefix 10.0.0.0/16 \
    --subnet-name $subnetName --subnet-prefix 10.0.0.0/24

# Create base NSG to simulate an existing NSG

az network nsg create -g $vnetRgName -n $nsgName

az network vnet subnet update \
    --resource-group $vnetRgName \
    --vnet-name $vnetName \
    --name $subnetName \
    --network-security-group $nsgName
    
#  NOTE! The VNET must always be in the same region as the Azure Image Builder service region.
```

### <a name="add-network-security-group-rule"></a>Hinzufügen einer Netzwerksicherheitsgruppen-Regel

Diese Regel ermöglicht Konnektivität zwischen dem Lastenausgleich des Azure VM Image Builder-Diensts und der Proxy-VM. Port 60001 dient für Linux-Betriebssysteme und Port 60000 für Windows-Betriebssysteme. Die Proxy-VM stellt eine Verbindung mit der Build-VM über Port 22 für Linux-Betriebssysteme oder Port 5986 für Windows-Betriebssysteme her.

```azurecli-interactive
az network nsg rule create \
    --resource-group $vnetRgName \
    --nsg-name $nsgName \
    -n AzureImageBuilderNsgRule \
    --priority 400 \
    --source-address-prefixes AzureLoadBalancer \
    --destination-address-prefixes VirtualNetwork \
    --destination-port-ranges 60000-60001 --direction inbound \
    --access Allow --protocol Tcp \
    --description "Allow Image Builder Private Link Access to Proxy VM"
```

### <a name="disable-private-service-policy-on-subnet"></a>Deaktivieren der Richtlinie für private Dienste im Subnetz

```azurecli-interactive
az network vnet subnet update \
  --name $subnetName \
  --resource-group $vnetRgName \
  --vnet-name $vnetName \
  --disable-private-link-service-network-policies true 
```

Weitere Informationen zu Azure VM Image Builder-Netzwerken finden Sie unter den [Netzwerkoptionen für den Azure VM Image Builder-Dienst](image-builder-networking.md).

## <a name="modify-the-example-template-and-create-role"></a>Ändern der Beispielvorlage und Erstellen einer Rolle

```bash
# download the example and configure it with your vars

curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1a_Creating_a_Custom_Linux_Image_on_Existing_VNET/existingVNETLinux.json -o existingVNETLinux.json
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" existingVNETLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" existingVNETLinux.json
sed -i -e "s/<region>/$location/g" existingVNETLinux.json
sed -i -e "s/<imageName>/$imageName/g" existingVNETLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" existingVNETLinux.json

sed -i -e "s/<vnetName>/$vnetName/g" existingVNETLinux.json
sed -i -e "s/<subnetName>/$subnetName/g" existingVNETLinux.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" existingVNETLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json

```

## <a name="set-permissions-on-the-resource-group"></a>Festlegen von Berechtigungen für die Ressourcengruppe

Image Builder verwendet die angegebene [Benutzeridentität](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity), um das Image in die Azure Shared Image Gallery (SIG) einzufügen. In diesem Beispiel erstellen Sie eine Azure-Rollendefinition, die über die präzisen Aktionen zur Verteilung des Images an die SIG verfügt. Die Rollendefinition wird dann der Benutzeridentität zugewiesen.

```bash
# create user assigned identity for image builder
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# update the template
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" existingVNETLinux.json

# make role name unique, to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# update the definitions
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json
```

Anstatt Image Builder eine geringere Granularität und höhere Berechtigungen zu gewähren, können Sie zwei Rollen erstellen. Eine Berechtigung ermöglicht dem Builder das Erstellen eines Images, die andere gestattet es ihm, die Build-VM und den Lastenausgleich mit Ihrem VNET zu verbinden.

```bash
# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json
az role definition create --role-definition ./aibRoleNetworking.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$vnetRgName
```

Weitere Informationen zu Berechtigungen finden Sie unter [Konfigurieren von Berechtigungen für den Azure Image Builder-Dienst mithilfe der Azure CLI](image-builder-permissions-cli.md) oder [Konfigurieren von Berechtigungen für den Azure Image Builder-Dienst mithilfe von PowerShell](image-builder-permissions-powershell.md).

## <a name="create-the-image"></a>Erstellen des Images

Senden Sie die Imagekonfiguration an den Azure Image Builder-Dienst.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @existingVNETLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n existingVNETLinuxTemplate01

# Wait approximately 1-3 mins (validation, permissions etc.)
```

Starten Sie den Imagebuild.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n existingVNETLinuxTemplate01 \
     --action Run 

# Wait approximately 15 mins
```

Das Erstellen des Images und Replizieren in beiden Regionen kann einige Zeit in Anspruch nehmen. Warten Sie den Abschluss des Vorgangs ab, bevor Sie eine VM erstellen.


## <a name="create-the-vm"></a>Erstellen des virtuellen Computers

Erstellen Sie eine VM aus der Imageversion, die von Azure Image Builder erstellt wurde.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm0001 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Stellen Sie eine SSH-Verbindung mit der VM her.

```bash
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


Im folgenden Schritt werden das erstellte Image und dessen Ressourcendateien gelöscht. Achten Sie darauf, dass Sie mit dieser Bereitstellung fertig sind, bevor Sie die Ressourcen löschen.

Wenn Sie die Imagekatalogressourcen löschen, müssen Sie alle Imageversionen löschen, bevor Sie die Imagedefinition löschen können, die für deren Erstellung verwendet wurde. Sie müssen zunächst alle Imagedefinitionen im Katalog löschen, um den Katalog löschen zu können.

Löschen Sie die Image Builder-Vorlage.

```azurecli
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n existingVNETLinuxTemplate01
```

Löschen Sie Berechtigungszuordnungen, Rollen und Identitäten.
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role assignment delete \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$vnetRgName


az role definition delete --name "$imageRoleDefName"
az role definition delete --name "$netRoleDefName"

az identity delete --ids $imgBuilderId
```

Löschen Sie die Ressourcengruppe.

```azurecli-interactive
az group delete -n $imageResourceGroup
```

Wenn Sie für diese Schnellstartanleitung ein VNET erstellt haben, können Sie es löschen, wenn es nicht mehr verwendet wird.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über [Azure-Kataloge mit freigegebenen Images](shared-image-galleries.md).