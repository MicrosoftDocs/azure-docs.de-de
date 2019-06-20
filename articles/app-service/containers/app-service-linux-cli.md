---
title: Verwalten von Web-App für Container mithilfe der Azure CLI – Azure App Service | Microsoft-Dokumentation
description: Verwalten von Web-App für Container mithilfe der Azure CLI.
keywords: Azure App Service, Web-App, CLI, Linux, OSS
services: app-service
documentationCenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: aelnably
ms.custom: seodec18
ms.openlocfilehash: 21f6963fbaada4524f27602454d38e7252a5e8b9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60850083"
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>Verwalten von Web-App für Container mithilfe der Azure CLI

Mit den Befehlen in diesem Artikel können Sie eine Web-App für Container mit der Azure CLI erstellen und verwalten.
Sie können die neue Version der Befehlszeilenschnittstelle auf zwei Arten einrichten:

* Durch [Installieren der Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) auf Ihrem Computer
* Mithilfe der [Azure Cloud Shell (Vorschau)](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>Erstellen eines Linux-App Service-Plans

Um einen Linux-App Service-Plan zu erstellen, können Sie den folgenden Befehl verwenden:

```azurecli-interactive
az appservice plan create -n appname -g rgname --is-linux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Erstellen einer benutzerdefinierten Docker-Container-Web-App

Zum Erstellen einer Web-App und Konfigurieren dieser für die Ausführung eines benutzerdefinierten Docker-Containers können Sie den folgenden Befehl verwenden:

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>Aktivieren der Protokollierung von Docker-Containern

Um die Protokollierung von Docker-Containern zu aktivieren, können Sie den folgenden Befehl verwenden:

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>Ändern des benutzerdefinierten Docker-Containers für eine vorhandene Web-App für Container-App

Wenn Sie eine bereits erstellte App vom aktuellen Docker-Image auf ein neues Image umstellen möchten, können Sie den folgenden Befehl verwenden:

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>Verwenden von Docker-Images aus einer privaten Registrierung

Sie können Ihre App für die Verwendung von Images aus einer privaten Registrierung konfigurieren. Sie müssen dabei die URL für Ihre Registrierung, den Benutzernamen und das Kennwort angeben. Dies kann mithilfe des folgenden Befehls durchgeführt werden:

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Aktivieren von Continuous Deployment für benutzerdefinierte Docker-Images

Mit dem folgenden Befehl aktivieren Sie die CD-Funktion und rufen die Webhook-URL ab. Diese URL kann zum Konfigurieren des DockerHubs oder Repositorys der Azure Container Registry verwendet werden.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>Erstellen einer Web-App für Container mit einem unserer integrierten Laufzeitframeworks

Verwenden Sie zum Erstellen einer Web-App für Container-App mit PHP 5.6 den folgenden Befehl.

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>Ändern der Frameworkversion für eine vorhandene Web-App für Container-App

Wenn Sie eine bereits erstellte App von der aktuellen Frameworkversion auf Node.js 6.11 umstellen möchten, können Sie den folgenden Befehl verwenden:

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Einrichten von Git-Bereitstellungen für Ihre Web-App

Zum Einrichten von Git-Bereitstellungen für Ihre App können Sie den folgenden Befehl verwenden:

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>Nächste Schritte

* [Was ist Azure App Service unter Linux?](app-service-linux-intro.md)
* [Installieren der Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Azure Cloud Shell (Vorschau)](../../cloud-shell/overview.md)
* [Einrichten von Stagingumgebungen in Azure App Service](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Continuous Deployment mit Web-App für Container](app-service-linux-ci-cd.md)
