---
title: Bereitstellen in Azure Container Instances aus Azure Container Registry
description: Erfahren Sie, wie Container in Azure Container Instances mithilfe von Containerimages in einer Azure Container Registry bereitgestellt werden.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 01/04/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 515dc8ed4a2fc9b3d2973d393c6894d8c7cef8f0
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729379"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Bereitstellen in Azure Container Instances aus Azure Container Registry

[Azure Container Registry](../container-registry/container-registry-intro.md) ist ein verwalteter Containerregistrierungsdienst auf Azure-Basis zum Speichern privater Docker-Containerimages. In diesem Artikel wird beschrieben, wie Containerimages, die in einer Azure-Containerregistrierung gespeichert sind, in Azure Container Instances bereitgestellt werden können.

## <a name="prerequisites"></a>Voraussetzungen

**Azure Container Registry**: Sie benötigen eine Azure-Containerregistrierung, die mindestens ein Containerimage enthält, um die Schritte in diesem Artikel auszuführen. Falls Sie eine Registrierung benötigen, siehe [Erstellen einer Containerregistrierung mit der Azure-Befehlszeilenschnittstelle (CLI)](../container-registry/container-registry-get-started-azure-cli.md).

**Azure CLI**: Die Befehlszeilenbeispiele in diesem Artikel verwenden die [Azure CLI](/cli/azure/) und sind für die Bash-Shell formatiert. Sie können die [Azure CLI lokal installieren](/cli/azure/install-azure-cli) oder die [Azure Cloud Shell][cloud-shell-bash] nutzen.

## <a name="configure-registry-authentication"></a>Konfigurieren der Authentifizierung der Registrierung

In Produktionsszenarien sollte der Zugriff auf eine Azure-Containerregistrierung mithilfe von [Dienstprinzipalen](../container-registry/container-registry-auth-service-principal.md) bereitgestellt werden. Dienstprinzipale ermöglichen Ihnen eine [rollenbasierte Steuerung des Zugriffs](../container-registry/container-registry-roles.md) auf Ihre Containerimages. Beispielsweise können Sie einen Dienstprinzipal mit ausschließlichem Pullzugriff auf eine Registrierung konfigurieren.

Im folgenden Abschnitt erstellen Sie einen Azure-Schlüsseltresor und Dienstprinzipal und speichern die Anmeldeinformationen des Dienstprinzipals im Tresor. 

### <a name="create-key-vault"></a>Erstellen eines Schlüsseltresors

Wenn Sie noch keinen Tresor in [Azure Key Vault](../key-vault/key-vault-overview.md) haben, erstellen Sie einen mithilfe der Azure CLI und folgenden Befehle.

Aktualisieren Sie die Variable `RES_GROUP` mit dem Namen einer vorhandenen Ressourcengruppe, in der der Schlüsselspeicher erstellt werden soll, und `ACR_NAME` mit dem Namen Ihrer Containerregistrierung. Geben Sie in `AKV_NAME` einen Namen für Ihren neuen Schlüsseltresor ein. Der Tresorname muss in Azure eindeutig sein und 3-24 alphanumerische Zeichen umfassen. Er muss mit einem Buchstaben beginnen, mit einem Buchstaben oder einer Ziffer enden und darf keine aufeinanderfolgenden Bindestriche enthalten.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Erstellen eines Dienstprinzipals und Speichern von Anmeldeinformationen

Sie müssen nun einen Dienstprinzipal erstellen und seine Anmeldeinformationen in Ihrem Schlüsselspeicher speichern.

Der folgende Befehl verwendet [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] zum Erstellen des Dienstprinzipals und [az keyvault secret set][az-keyvault-secret-set] zum Speichern des **Passworts** des Dienstprinzipals im Tresor.

```azurecli
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name http://$ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

Das Argument `--role` im vorhergehenden Befehl konfiguriert den Dienstprinzipal mit der Rolle *acrpull*, die ihm ausschließlich Pullzugriff auf die Registrierung gewährt. Um sowohl Push- als auch Pullzugriff zu gewähren, ändern Sie das Argument `--role` in *acrpush*.

Speichern Sie als Nächstes die *appId* des Dienstprinzipals im Tresor. Dies ist der **Benutzername**, den Sie zur Authentifizierung an Azure Container Registry übergeben.

```azurecli
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Sie haben einen Azure Key Vault erstellt und in ihm zwei Geheimnisse gespeichert:

* `$ACR_NAME-pull-usr`: Die Dienstprinzipal-ID für die Verwendung als **Benutzername** für die Containerregistrierung.
* `$ACR_NAME-pull-pwd`: Das Kennwort des Dienstprinzipals für die Verwendung als **Kennwort** für die Containerregistrierung.

Sie können nun auf diese Geheimnisse anhand des Namens verweisen, wenn Sie oder Ihre Anwendungen und Dienste Images per Pull aus der Registrierung abrufen.

## <a name="deploy-container-with-azure-cli"></a>Bereitstellen des Containers mit der Azure CLI

Jetzt, da die Anmeldeinformationen des Dienstprinzials in Azure Key Vault-Geheimnissen gespeichert sind, können Ihre Anwendungen und Dienste diese verwenden, um auf Ihre private Registrierung zuzugreifen.

Rufen Sie zuerst den Namen des Anmeldeservers der Registrierung mit dem Befehl [az acr show][az-acr-show] auf. Der Name des Anmeldeservers besteht aus Kleinbuchstaben und ähnelt `myregistry.azurecr.io`.

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

Führen Sie den folgenden [az container create][az-container-create]-Befehl aus, um eine Containerinstanz bereitzustellen. Der Befehl verwendet die in Azure Key Vault gespeicherten Anmeldeinformationen des Dienstprinzials, um sich bei Ihrer Containerregistrierung zu authentifizieren, und geht davon aus, dass Sie zuvor das Image [aci-helloworld](container-instances-quickstart.md) per Push in Ihre Registrierung verschoben haben. Aktualisieren Sie den Wert `--image`, wenn Sie ein anderes Image aus Ihrer Registrierung verwenden möchten.

```azurecli
az container create \
    --name aci-demo \
    --resource-group $RES_GROUP \
    --image $ACR_LOGIN_SERVER/aci-helloworld:v1 \
    --registry-login-server $ACR_LOGIN_SERVER \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label aci-demo-$RANDOM \
    --query ipAddress.fqdn
```

Der Wert `--dns-name-label` muss innerhalb von Azure eindeutig sein, weshalb der vorherige Befehl eine Zufallszahl an die DNS-Namenskennung des Containers anfügt. Die Ausgabe des Befehls zeigt den vollqualifizierten Domänennamen (FQDN) des Containers. Beispiel:

```console
$ az container create --name aci-demo --resource-group $RES_GROUP --image $ACR_LOGIN_SERVER/aci-helloworld:v1 --registry-login-server $ACR_LOGIN_SERVER --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) --dns-name-label aci-demo-$RANDOM --query ipAddress.fqdn
"aci-demo-25007.eastus.azurecontainer.io"
```

Nachdem der Container erfolgreich gestartet wurde, können Sie in Ihrem Browser zu seinem FQDN navigieren, um zu überprüfen, ob die Anwendung erfolgreich ausgeführt wird.

## <a name="deploy-with-azure-resource-manager-template"></a>Bereitstellen mit einer Azure Resource Manager-Vorlage

Sie können die Eigenschaften von Azure Container Registry in einer Azure Resource Manager-Vorlage angeben, indem Sie die `imageRegistryCredentials`-Eigenschaft in die Definition der Containergruppe einbeziehen:

```JSON
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

Weitere Informationen zum Verweisen auf Azure Key Vault-Geheimnisse in einer Resource Manager-Vorlage finden Sie unter [Verwenden von Azure Key Vault zum Übergeben eines sicheren Parameterwerts während der Bereitstellung](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Bereitstellen über das Azure-Portal

Wenn Sie Containerimages in einer Azure Container Registry verwalten, können Sie im Azure-Portal in Azure Container Instances auf einfache Weise einen Container erstellen. Wenn Sie das Portal verwenden, um eine Containerinstanz aus einer Container Registry bereitzustellen, müssen Sie das [Administratorkonto](../container-registry/container-registry-authentication.md#admin-account) der Registrierung aktivieren. Das Administratorkonto ist dafür ausgelegt, dass ein einzelner Benutzer auf die Registrierung zugreift (hauptsächlich für Testzwecke). 

1. Navigieren Sie im Azure-Portal wieder zu Ihrer Containerregistrierung.

1. Um sicherzustellen, dass das Administratorkonto aktiviert ist, wählen Sie zunächst **Zugriffsschlüssel** und dann unter **Administratorbenutzer** die Option **Aktivieren** aus.

1. Klicken Sie auf **Repositorys**, und wählen Sie dann das Repository aus, über das die Bereitstellung erfolgen soll. Klicken Sie mit der rechten Maustaste auf das Tag für das Containerimage, das Sie bereitstellen möchten, und klicken Sie auf **Instanz ausführen**.

    ![„Instanz ausführen“ in Azure Container Registry im Azure-Portal][acr-runinstance-contextmenu]

1. Geben Sie einen Namen für den Container und einen Namen für die Ressourcengruppe ein. Sie können den Standardwert auch bei Bedarf ändern.

    ![Erstellmenü für Azure Container Instances][acr-create-deeplink]

1. Sobald die Bereitstellung abgeschlossen wurde, können Sie vom Benachrichtigungsbereich aus zur Containergruppe navigieren, um die IP-Adresse und andere Eigenschaften zu sehen.

    ![Detailansicht für eine Containergruppe in Azure Container Instances][aci-detailsview]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure Container Registry-Authentifizierung finden Sie unter [Authentifizieren bei einer Azure-Containerregistrierung](../container-registry/container-registry-authentication.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-try-it]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az-container-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
