---
title: Konfigurieren von Berechtigungen für den Azure VM Image Builder-Dienst mithilfe der Azure CLI
description: Konfigurieren von Anforderungen für den Azure VM Image Builder-Dienst einschließlich Berechtigungen und Rechten mithilfe der Azure CLI
author: cynthn
ms.author: danis
ms.date: 05/06/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: f598ed3e2aef2f53b90b213d0bd1b002f56bd30c
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517749"
---
# <a name="configure-azure-image-builder-service-permissions-using-azure-cli"></a>Konfigurieren von Berechtigungen für den Azure VM Image Builder-Dienst mithilfe der Azure CLI

Für den Azure VM Image Builder-Dienst müssen Berechtigungen und Rechte konfiguriert werden, bevor ein Image erstellt wird. In den folgenden Abschnitten erfahren Sie, wie mögliche Szenarios mithilfe der Azure CLI konfiguriert werden.

> [!IMPORTANT]
> Azure Image Builder ist derzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="register-the-features"></a>Registrieren des Features

Sie müssen sich zunächst für den Azure VM Image Builder-Dienst registrieren. Die Registrierung gewährt Ihnen die Dienstberechtigungen zum Erstellen, Verwalten und Löschen einer Ressourcengruppe für das Staging. Der Dienst verfügt auch über die Berechtigung, der Gruppe Ressourcen hinzuzufügen, die für das Erstellen des Images erforderlich sind.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## <a name="create-an-azure-user-assigned-managed-identity"></a>Erstellen einer in Azure benutzerseitig zugewiesenen verwalteten Identität

Im Azure VM Image Builder-Dienst müssen Sie eine [in Azure benutzerseitig zugewiesene verwaltete Identität](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) erstellen. Der Azure VM Image Builder-Dienst verwendet die benutzerseitig zugewiesene verwaltete Identität, um Images zu lesen, Images zu schreiben und auf Speicherkonten in Azure zuzugreifen. Sie gewähren der Identität die Berechtigung, bestimmte Aktionen für Ihr Abonnement ausführen zu dürfen.

> [!NOTE]
> Bisher hat der Azure VM Image Builder-Dienst die Dienstprinzipalnamen (Service Principal Name, SPN) des Azure VM Image Builder-Diensts verwendet, um Imageressourcengruppen Berechtigungen zu gewähren. Die Verwendung des SPN wird eingestellt. Verwenden Sie stattdessen eine benutzerseitig zugewiesene verwaltete Identität.

Im folgenden Beispiel sehen Sie, wie Sie eine in Azure benutzerseitig zugewiesene verwaltete Identität erstellen. Ersetzen Sie die Platzhaltereinstellungen, um Ihre Variablen festzulegen.

| Einstellung | BESCHREIBUNG |
|---------|-------------|
| \<Resource group\> | Hierbei handelt es sich um die Ressourcengruppe, in der die benutzerseitig verwaltete Identität erstellt werden soll. |

```azurecli-interactive
identityName="aibIdentity"
imageResourceGroup=<Resource group>

az identity create \
    --resource-group $imageResourceGroup \
    --name $identityName
```

Weitere Informationen zu in Azure benutzerseitig zugewiesenen Identitäten finden Sie in der Dokumentation [Erstellen, Auflisten oder Löschen einer vom Benutzer zugewiesenen verwalteten Identität mithilfe der Azure-Befehlszeilenschnittstelle](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) im Abschnitt zum Erstellen einer Identität.

## <a name="allow-image-builder-to-distribute-images"></a>Berechtigungen für den Azure VM Image Builder-Dienst zum Verteilen von Images

Damit der Azure VM Image Builder-Dienst Images verteilen kann (Verwaltete Images/Shared Image Gallery), muss der Azure VM Image Builder-Dienst die Berechtigung haben, die Images in diese Ressourcengruppen einzufügen. Zum Gewähren der erforderlichen Berechtigungen müssen Sie eine benutzerseitig zugewiesene verwaltete Identität erstellen und ihr Rechte für die Ressourcengruppe erteilen, in der das Image erstellt wird. Der Azure VM Image Builder-Dienst hat **keine** Berechtigung, auf Ressourcen in anderen Ressourcengruppen des Abonnements zuzugreifen. Sie müssen explizite Aktionen ausführen, um den Zugriff zu gestatten, damit für Ihre Builds keine Fehler auftreten.

Sie müssen der benutzerseitig zugewiesenen verwalteten Identität keine Berechtigungen eines Mitwirkenden für die Ressourcengruppe zuweisen, damit Images verteilt werden können. Die benutzerseitig zugewiesene verwaltete Identität benötigt jedoch die folgenden `Actions`-Berechtigungen in Azure in der Verteilungsressourcengruppe:

```Actions
Microsoft.Compute/images/write
Microsoft.Compute/images/read
Microsoft.Compute/images/delete
```

Für die Verteilung an eine Shared Image Gallery-Instanz benötigen Sie außerdem Folgendes:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
Microsoft.Compute/galleries/images/versions/write
```

## <a name="permission-to-customize-existing-images"></a>Berechtigung zum Anpassen vorhandener Images

Damit der Azure VM Image Builder-Dienst Images aus benutzerdefinierten Quellimages erstellen kann (Verwaltete Images/Image Gallery), muss der Azure VM Image Builder-Dienst die Berechtigung haben, die Images in diesen Ressourcengruppen zu lesen. Zum Gewähren der erforderlichen Berechtigungen müssen Sie eine benutzerseitig zugewiesene verwaltete Identität erstellen und ihr Rechte für die Ressourcengruppe erteilen, in der sich das Image befindet.

So erstellen Sie ein Image aus einem vorhandenen benutzerdefinierten Image:

```Actions
Microsoft.Compute/galleries/read
```

So erstellen Sie ein Image aus einer vorhandenen Shared Image Gallery-Version:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
```

## <a name="permission-to-customize-images-on-your-vnets"></a>Berechtigung zum Anpassen von Images in Ihren VNETs

Der Azure VM Image Builder-Dienst hat die Funktion, ein vorhandenes VNET in Ihrem Abonnement bereitzustellen und zu verwenden. Insofern ist der Zugriff für Anpassungen für verknüpfte Ressourcen möglich.

Sie müssen der benutzerseitig zugewiesenen verwalteten Identität keine Berechtigungen eines Mitwirkenden für die Ressourcengruppe zuweisen, damit eine VM für ein vorhandenes VNET bereitgestellt werden kann. Die benutzerseitig zugewiesene verwaltete Identität benötigt jedoch die folgenden `Actions`-Berechtigungen in Azure für die VNET-Ressourcengruppe:

```Actions
Microsoft.Network/virtualNetworks/read
Microsoft.Network/virtualNetworks/subnets/join/action
```

## <a name="create-an-azure-role-definition"></a>Erstellen einer Rollendefinition in Azure

Die folgenden Beispiele erstellen eine Rollendefinition in Azure auf Grundlage der Aktionen, die in den vorherigen Abschnitten beschrieben wurden. Diese Beispiele werden auf Ebene der Ressourcengruppe angewendet. Bewerten und testen Sie, ob die Granularität der Beispiele ausreichend für Ihre Anforderungen ist. Für Ihr Szenario ist möglicherweise eine genauere Abstimmung für eine bestimmte Shared Image Gallery-Instanz nötig.

Die Aktionen für Images ermöglichen Lese- und Schreibvorgänge. Entscheiden Sie, was für Ihre Umgebung geeignet ist. Erstellen Sie beispielsweise eine Rolle, die es dem Azure VM Image Builder-Dienst ermöglicht, Images aus der Ressourcengruppe *example-rg-1* zu lesen und Images in die Ressourcengruppe *example-rg-2* zu schreiben.

### <a name="custom-image-azure-role-example"></a>Beispiel für eine benutzerdefinierte Rolle für ein Image in Azure

Im folgenden Beispiel wird eine Azure-Rolle erstellt, die für Verwendung und Verteilung eines benutzerdefinierten Quellimages verwendet wird. Dann weisen Sie die benutzerdefinierte Rolle der benutzerseitig zugewiesenen verwalteten Identität für den Azure VM Image Builder-Dienst zu.

Zur Vereinfachung der Ersetzung der Werte im Beispiel legen Sie die folgenden Variablen zuerst fest. Ersetzen Sie die Platzhaltereinstellungen, um Ihre Variablen festzulegen.

| Einstellung | BESCHREIBUNG |
|---------|-------------|
| \<Subscription ID\> | ID Ihres Azure-Abonnements |
| \<Resource group\> | Ressourcengruppe für benutzerdefiniertes Image |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
# Resource group - For Preview, image builder will only support creating custom images in the same Resource Group as the source managed image.
imageResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# Create a custom role from the sample aibRoleImageCreation.json description file.
az role definition create --role-definition ./aibRoleImageCreation.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

### <a name="existing-vnet-azure-role-example"></a>Beispiel für eine vorhandene VNET-Rolle in Azure

Im folgenden Beispiel wird eine Azure-Rolle erstellt, die für Verwendung und Verteilung eines vorhandenen VNET-Images verwendet wird. Dann weisen Sie die benutzerdefinierte Rolle der benutzerseitig zugewiesenen verwalteten Identität für den Azure VM Image Builder-Dienst zu.

Zur Vereinfachung der Ersetzung der Werte im Beispiel legen Sie die folgenden Variablen zuerst fest. Ersetzen Sie die Platzhaltereinstellungen, um Ihre Variablen festzulegen.

| Einstellung | BESCHREIBUNG |
|---------|-------------|
| \<Subscription ID\> | ID Ihres Azure-Abonnements |
| \<Resource group\> | VNET-Ressourcengruppe |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
VnetResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json

# Create a unique role name to avoid clashes in the same domain
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json

# Create a custom role from the aibRoleNetworking.json description file.
az role definition create --role-definition ./aibRoleNetworking.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$VnetResourceGroup
```

## <a name="using-managed-identity-for-azure-storage-access"></a>Verwenden der verwalteten Identität für den Zugriff auf Azure Storage

Wenn Sie eine nahtlose Authentifizierung mit Azure Storage und die Verwendung privater Container wünschen, benötigen Azure VM Image Builder und Azure eine benutzerseitig zugewiesene verwaltete Identität. Azure VM Image Builder verwenden die Identität für die Authentifizierung mit Azure Storage.

> [!NOTE]
> Azure VM Image Builder verwendet die Identität nur zum Zeitpunkt der Übermittlung der Imagevorlage. Die erstellte VM verfügt während der Imageerstellung nicht über Zugriff auf die Identität.

Verwenden Sie die Azure CLI, um die benutzerseitig zugewiesene verwaltete Identität zu erstellen.

```azurecli
az role assignment create \
    --assignee <Image Builder client ID> \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/<Storage account container>
```

In der Azure VM Image Builder-Vorlage müssen Sie die benutzerseitig zugewiesene verwaltete Identität angeben.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
    "location": "<Region>",
    ..
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<Image Builder ID>": {}     
        }
```

Ersetzen Sie die folgenden Platzhaltereinstellungen:

| Einstellung | BESCHREIBUNG |
|---------|-------------|
| \<Region\> | Region der Vorlage |
| \<Resource group\> | Resource group |
| \<Storage account container\> | Name des Containers mit dem Speicherkonto |
| \<Subscription ID\> | Azure-Abonnement |

Weitere Informationen zur Verwendung einer benutzerseitig zugewiesenen verwalteten Identität finden Sie unter [Erstellen eines benutzerdefinierten Images mit einer benutzerseitig zugewiesenen verwaltete Identität in Azure für den nahtlosen Zugriff auf Azure Storage-Dateien](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage#create-a-custom-image-that-will-use-an-azure-user-assigned-managed-identity-to-seemlessly-access-files-azure-storage). In diesem Schnellstart durchlaufen Sie das Erstellen und Konfigurieren der benutzerseitig zugewiesenen verwalteten Identität für den Zugriff auf ein Speicherkonto.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Übersicht über Azure Image Builder](image-builder-overview.md).