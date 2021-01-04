---
title: CI/CD für benutzerdefinierte Linux-Container
description: Erfahren Sie, wie Sie Continuous Deployment für einen benutzerdefinierten Linux-Container in Azure App Service einrichten. Continuous Deployment wird für Docker Hub und ACR unterstützt.
keywords: Azure App Service, Linux, Docker, ACR, OSS
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: e879d8370821ea465147e344d0fe95836c843ff4
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008009"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Continuous Deployment mit Web-App für Container

In diesem Tutorial konfigurieren Sie Continuous Deployment für ein benutzerdefiniertes Containerimage aus verwalteten [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)-Repositorys oder aus [Docker Hub](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Aktivieren von Continuous Deployment mit ACR

![Screenshot des ACR-Webhooks](./media/deploy-ci-cd-custom-container/ci-cd-acr-02.png)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie auf der Seite links die Option **App Service** .
3. Klicken Sie auf den Namen der App, für die Sie Continuous Deployment konfigurieren möchten.
4. Wählen Sie auf der Seite **Containereinstellungen** die Option **Einzelner Container** aus.
5. Wählen Sie **Azure Container Registry** aus.
6. Wählen Sie **Continuous Deployment > Ein**.
7. Wählen Sie **Speichern**, um Continuous Deployment zu aktivieren.

## <a name="use-the-acr-webhook"></a>Verwenden des ACR-Webhooks

Nachdem Continuous Deployment aktiviert wurde, können Sie den neu erstellten Webhook auf Ihrer Webhooks-Seite in Azure Container Registry anzeigen.

![Screenshot, der zeigt, wo Sie den neu erstellten Webhook auf Ihrer Webhooks-Seite in Azure Container Registry anzeigen können.](./media/deploy-ci-cd-custom-container/ci-cd-acr-03.png)

Klicken Sie in Ihrer Container Registry-Instanz auf „Webhooks“, um die aktuellen Webhooks anzuzeigen.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Aktivieren von Continuous Deployment mit Docker Hub (optional)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie auf der Seite links die Option **App Service** .
3. Klicken Sie auf den Namen der App, für die Sie Continuous Deployment konfigurieren möchten.
4. Wählen Sie auf der Seite **Containereinstellungen** die Option **Einzelner Container** aus.
5. Wählen Sie **Docker Hub**.
6. Wählen Sie **Continuous Deployment > Ein**.
7. Wählen Sie **Speichern**, um Continuous Deployment zu aktivieren.

![Screenshot der App-Einstellung](./media/deploy-ci-cd-custom-container/ci-cd-docker-02.png)

Kopieren Sie die Webhook-URL. Zum Hinzufügen eines Webhooks für Docker Hub gehen Sie gemäß der Anleitung unter <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">Webhooks for Docker Hub</a> (Webhooks für Docker Hub) vor.

## <a name="automate-with-cli"></a>Automatisieren mithilfe der Befehlszeilenschnittstelle

Führen Sie zum Konfigurieren von CI/CD mithilfe der Azure CLI den Befehl [az webapp deployment container config](/cli/azure/webapp/deployment/container#az-webapp-deployment-container-config) aus, um die Webhook-URL zu generieren. Die URL kann zum Konfigurieren der DockerHub- oder Azure Container Registry-Instanz verwendet werden.

```azurecli-interactive
az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true
```

## <a name="next-steps"></a>Nächste Schritte

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Erstellen einer .NET Core-Web-App in App Service unter Linux](quickstart-dotnetcore.md?pivots=platform-linux)
* [Erstellen einer Ruby-App in App Service unter Linux](quickstart-ruby.md)
* [Schnellstart: Ausführen eines benutzerdefinierten Containers in App Service](quickstart-custom-container.md?pivots=container-linux)
* [App Service unter Linux – häufig gestellte Fragen](faq-app-service-linux.md)
* [Konfigurieren benutzerdefinierter Linux-Container](configure-custom-container.md)
