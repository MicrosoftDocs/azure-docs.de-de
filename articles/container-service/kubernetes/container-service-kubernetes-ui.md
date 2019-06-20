---
title: (VERALTET) Verwalten von Azure-Kubernetes-Clustern mit der Webbenutzeroberfläche
description: Verwenden der Webbenutzeroberfläche von Kubernetes in Azure Container Service
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: c3a79b2e4fab807613a54d2792f5f5b97570293b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60309660"
---
# <a name="deprecated-using-the-kubernetes-web-ui-with-azure-container-service"></a>(VERALTET) Verwenden der Webbenutzeroberfläche von Kubernetes mit Azure Container Service

> [!TIP]
> Die aktualisierte Version dieses Artikels, in der Azure Kubernetes Service verwendet wird, finden Sie unter [Zugreifen auf das Kubernetes-Webdashboard in Azure Kubernetes Service (AKS)](../../aks/kubernetes-dashboard.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Voraussetzungen
In dieser exemplarischen Vorgehensweise wird davon ausgegangen, dass Sie mit [Azure Container Service einen Kubernetes-Cluster erstellt haben](container-service-kubernetes-walkthrough.md).


Außerdem wird angenommen, dass die Azure CLI und die Tools `kubectl` installiert sind.

Führen Sie Folgendes aus, um zu prüfen, ob das Tool `az` installiert ist:

```console
$ az --version
```

Wenn das Tool `az` nicht installiert ist, finden Sie [hier](https://github.com/azure/azure-cli#installation) Anweisungen.

Führen Sie Folgendes aus, um zu prüfen, ob das Tool `kubectl` installiert ist:

```console
$ kubectl version
```

Wenn `kubectl` nicht installiert ist, können Sie folgenden Befehl ausführen:

```console
$ az acs kubernetes install-cli
```

## <a name="overview"></a>Übersicht

### <a name="connect-to-the-web-ui"></a>Herstellen einer Verbindung mit der Webbenutzeroberfläche
Sie können die Kubernetes-Webbenutzeroberfläche starten, indem Sie Folgendes ausführen:

```console
$ az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

Ein Webbrowser sollte geöffnet werden, der für die Kommunikation mit einem sicheren Proxy konfiguriert ist, der Ihren lokalen Computer mit der Kubernetes-Webbenutzeroberfläche verbindet.

### <a name="create-and-expose-a-service"></a>Erstellen und Bereitstellen eines Diensts
1. Klicken Sie auf der Webbenutzeroberfläche von Kubernetes im Fenster rechts oben auf die Schaltfläche **Create**.

    ![Kubernetes-Benutzeroberfläche mit Schaltfläche „Erstellen“](./media/container-service-kubernetes-ui/create.png)

    Ein Dialogfeld wird geöffnet, in dem Sie mit dem Erstellen Ihrer Anwendung beginnen können.

2. Geben Sie ihr den Namen `hello-nginx`. Verwenden Sie den [`nginx`-Container aus Docker](https://hub.docker.com/_/nginx/), und stellen Sie drei Replikate für diesen Webdienst bereit.

    ![Kubernetes-Pod-Dialogfeld „Create“](./media/container-service-kubernetes-ui/nginx.png)

3. Wählen Sie unter **Service** **External** aus, und geben Sie für „Port“ 80 ein.

    Diese Einstellung schafft einen Lastenausgleich für den Datenverkehr zu den drei Replikaten.

    ![Kubernetes-Dialogfeld für die Diensterstellung](./media/container-service-kubernetes-ui/service.png)

4. Klicken Sie auf **Deploy**, um diese Container und Dienste bereitzustellen.

    ![Kubernetes-Schaltfläche „Deploy“](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>Anzeigen Ihrer Container
Nach Klicken auf **Deploy** wird auf der Benutzeroberfläche eine Ansicht Ihres bereitgestellten Diensts gezeigt:

![Kubernetes-Status](./media/container-service-kubernetes-ui/status.png)

Sie können den Status der einzelnen Kubernetes-Objekte links auf der Benutzeroberfläche unter **Pods** anhand des Kreises erkennen. Wen ein Kreis nur teilweise vollständig ist, wird das Objekt noch bereitgestellt. Wenn ein Objekt vollständig bereitgestellt ist, wird ein grünes Häkchen angezeigt:

![Anzeige des Kubernetes-Bereitstellungsstatus](./media/container-service-kubernetes-ui/deployed.png)

Sobald alles ausgeführt wird, klicken Sie auf einen Ihrer Pods, um Einzelheiten zum ausgeführten Webdienst anzuzeigen.

![Kubernetes-Pods](./media/container-service-kubernetes-ui/pods.png)

In der Ansicht **Pods** werden Informationen zu den Containern im Pod sowie zu den von diesen Containern verwendeten CPU- und Arbeitsspeicherressourcen gezeigt:

![Kubernetes-Ressourcen](./media/container-service-kubernetes-ui/resources.png)

Wenn die Ressourcen nicht angezeigt werden, müssen Sie möglicherweise einige Minuten warten, bis die Überwachungsdaten weitergegeben wurden.

Um die Protokolle für den Container anzuzeigen, klicken Sie auf **View logs**.

![Kubernetes-Protokolle](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>Anzeigen Ihres Diensts
Zusätzlich zur Ausführung Ihrer Container hat die Kubernetes-Benutzeroberfläche einen externen `Service` erstellt, der einen Lastenausgleich für den Datenverkehr zu den Containern in Ihrem Cluster bereitstellt.

Klicken Sie im linken Navigationsbereich auf **Services**, um alle Dienste anzuzeigen (es sollte nur einer vorhanden sein).

![Kubernetes-Dienste](./media/container-service-kubernetes-ui/service-deployed.png)

In dieser Ansicht sollte ein externer Endpunkt (IP-Adresse) angezeigt werden, der Ihrem Dienst zugeordnet ist.
Wenn Sie auf diese IP-Adresse klicken, sollte Ihr Nginx-Container angezeigt werden, der hinter dem Lastenausgleich ausgeführt wird.

![nginx-Ansicht](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>Ändern der Größe Ihres Diensts
Zusätzlich zum Anzeigen der Objekte auf der Benutzeroberfläche können Sie die Kubernetes-API-Objekte bearbeiten und aktualisieren.

Klicken Sie zuerst im linken Navigationsbereich auf **Deployments**, um die Bereitstellung für Ihren Dienst anzuzeigen.

Sobald Sie in dieser Ansicht sind, klicken Sie auf die Replikatgruppe und dann in der oberen Navigationsleiste auf **Edit**:

![Kubernetes-Schaltfläche „Edit“](./media/container-service-kubernetes-ui/edit.png)

Setzen Sie das Feld `spec.replicas` auf `2`, und klicken Sie auf **Update**.

Dadurch sinkt die Anzahl der Replikate um zwei, indem einer Ihrer Pods gelöscht wird.

 

