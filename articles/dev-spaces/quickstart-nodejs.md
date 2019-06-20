---
title: Entwickeln mit Node.js unter Kubernetes mithilfe von Azure Dev Spaces
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
ms.author: zarhoads
ms.date: 03/22/2019
ms.topic: quickstart
description: Schnelle Kubernetes-Entwicklung mit Containern, Microservices und Node.js in Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Container, Helm, Service Mesh, Service Mesh-Routing, kubectl, k8s
manager: jeconnoc
ms.openlocfilehash: 5efacc38ab6b30a1a4ae45772f2b81030e76eb83
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393929"
---
# <a name="quickstart-develop-with-nodejs-on-kubernetes-using-azure-dev-spaces"></a>Schnellstart: Entwickeln mit Node.js unter Kubernetes mithilfe von Azure Dev Spaces

In diesem Leitfaden lernen Sie Folgendes:

- Einrichten von Azure Dev Spaces mit einem verwalteten Kubernetes-Cluster in Azure
- Iteratives Entwickeln von Code in Containern mit Visual Studio Code und der Befehlszeile
- Debuggen des Codes in Ihrem Entwicklerbereich über Visual Studio Code

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Falls Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen.
- [Installation von Visual Studio Code](https://code.visualstudio.com/download).
- Installation der [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)-Erweiterung für Visual Studio Code.
- [Azure-CLI installiert](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Erstellen eines Azure Kubernetes Service-Clusters

Sie müssen in einer [unterstützten Region][supported-regions] einen AKS-Cluster erstellen. Mit den unten angegebenen Befehlen wird eine Ressourcengruppe mit dem Namen *MyResourceGroup* und der AKS-Cluster *MyAKS* erstellt.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Aktivieren von Azure Dev Spaces in Ihrem AKS-Cluster

Verwenden Sie den Befehl `use-dev-spaces`, um Dev Spaces in Ihrem AKS-Cluster zu aktivieren, und befolgen Sie die angezeigten Anweisungen. Mit dem unten angegebenen Befehl wird Dev Spaces im Cluster *MyAKS* in der Gruppe *MyResourceGroup* aktiviert und der Entwicklerbereich *default* erstellt.

```cmd
$ az aks use-dev-spaces -g MyResourceGroup -n MyAKS

'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Abrufen des Codes für die Beispielanwendung

In diesem Artikel verwenden Sie die [Azure Dev Spaces-Beispielanwendung](https://github.com/Azure/dev-spaces), um die Nutzung von Azure Dev Spaces zu veranschaulichen.

Klonen Sie die Anwendung über GitHub, und navigieren Sie zum Verzeichnis *dev-spaces/samples/nodejs/getting-started/webfrontend*:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Vorbereiten der Anwendung

Generieren Sie die Docker- und Helm-Diagrammobjekte zum Ausführen der Anwendung in Kubernetes mit dem Befehl `azds prep`:

```cmd
azds prep --public
```

Sie müssen den Befehl `prep` im Verzeichnis *dev-spaces/samples/nodejs/getting-started/webfrontend* ausführen, um die Docker- und Helm-Diagrammobjekte richtig zu generieren.

## <a name="build-and-run-code-in-kubernetes"></a>Erstellen und Ausführen von Code in Kubernetes

Verwenden Sie den Befehl `azds up`, um Ihren Code in AKS zu erstellen und auszuführen:

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
Step 1/8 : FROM node
Step 2/8 : ENV PORT 80
Step 3/8 : EXPOSE 80
Step 4/8 : WORKDIR /app
Step 5/8 : COPY package.json .
Step 6/8 : RUN npm install
Step 7/8 : COPY . .
Step 8/8 : CMD ["npm", "start"]
Built container image in 6m 17s
Waiting for container...13s
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:54256
...
```

Sie können die Ausführung des Diensts verfolgen, indem Sie die öffentliche URL öffnen, die in der Ausgabe des Befehls `azds up` angezeigt wird. In diesem Beispiel lautet die öffentliche URL *http://webfrontend.1234567890abcdef1234.eus.azds.io/* .

Wenn Sie die Ausführung des Befehls `azds up` mit *STRG+C* anhalten, wird der Dienst in AKS weiter ausgeführt, und die öffentliche URL bleibt verfügbar.

## <a name="update-code"></a>Aktualisieren des Codes

Zum Bereitstellen einer aktualisierten Version Ihres Diensts können Sie Dateien in Ihrem Projekt aktualisieren und den Befehl `azds up` erneut ausführen. Beispiel:

1. Drücken Sie *STRG+C*, wenn `azds up` noch ausgeführt wird.
1. Aktualisieren Sie [Zeile 10 unter `server.js`](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L10) in:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Speichern Sie die Änderungen.
1. Führen Sie den Befehl `azds up` erneut aus:

    ```cmd
    $ azds up
    Using dev space 'default' with target 'MyAKS'
    Synchronizing files...1s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Navigieren Sie zu Ihrem ausgeführten Dienst, und sehen Sie sich Ihre Änderungen an.
1. Drücken Sie *STRG+C*, um den Befehl `azds up` zu beenden.

## <a name="initialize-code-for-debugging-in-kubernetes-with-visual-studio-code"></a>Initialisieren von Code zum Debuggen in Kubernetes mit Visual Studio Code

Öffnen Sie Visual Studio Code, klicken Sie auf *Datei* und dann auf *Öffnen...* , navigieren Sie zum Verzeichnis *dev-spaces/samples/nodejs/getting-started/webfrontend*, und klicken Sie auf *Öffnen*.

Das Projekt *webfrontend* ist jetzt in Visual Studio Code geöffnet. Dies ist derselbe Dienst, den Sie mit dem Befehl `azds up` ausgeführt haben. Sie müssen dieses Projekt so vorbereiten, dass Visual Studio Code für die Kommunikation mit Ihrem Entwicklerbereich verwendet wird, um diesen Dienst in AKS mit Visual Studio Code und nicht direkt mit `azds up` zu debuggen.

Klicken Sie zum Öffnen der Befehlspalette in Visual Studio Code auf *Ansicht* und dann auf *Befehlspalette*. Beginnen Sie mit dem Eingeben von `Azure Dev Spaces`, und klicken Sie auf `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.

![](./media/common/command-palette.png)

Mit diesem Befehl wird Ihr Projekt für die Ausführung in Azure Dev Spaces direkt aus Visual Studio Code vorbereitet. Außerdem wird das Verzeichnis *.vscode* mit der grundlegenden Debugkonfiguration Ihres Projekts generiert.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Erstellen und Ausführen von Code in Kubernetes über Visual Studio Code

Klicken Sie links auf das Symbol *Debuggen* und dann oben auf *Launch Server (AZDS)* (Server starten (AZDS)).

![](media/get-started-node/debug-configuration-nodejs.png)

Mit diesem Befehl wird Ihr Dienst in Azure Dev Spaces im Debugmodus erstellt und ausgeführt. Im Fenster *Terminal* am unteren Rand werden die Buildausgabe und die URLs zur Ausführung von Azure Dev Spaces für Ihren Dienst angezeigt. In der *Debugging-Konsole* wird die Protokollausgabe angezeigt.

> [!Note]
> Falls in der *Befehlspalette* keine Azure Dev Spaces-Befehle angezeigt werden, sollten Sie überprüfen, ob die [Visual Studio Code-Erweiterung für Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) installiert wurde. Vergewissern Sie sich auch, dass Sie das Verzeichnis *dev-spaces/samples/nodejs/getting-started/webfrontend* in Visual Studio Code geöffnet haben.

Klicken Sie auf *Debuggen* und dann auf *Debuggen beenden*, um den Debugger zu beenden.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Festlegen und Verwenden von Haltepunkten für das Debuggen

Starten Sie Ihren Dienst, indem Sie *Launch Server (AZDS)* (Server starten (AZDS)) verwenden.

Navigieren Sie zurück zur Ansicht *Explorer*, indem Sie auf *Ansicht* und dann auf *Explorer* klicken. Öffnen Sie `server.js`, und klicken Sie in Zeile 10, um den Cursor darin zu platzieren. Drücken Sie zum Festlegen eines Haltepunkts *F9*, oder klicken Sie auf *Debuggen* und dann auf *Haltepunkt umschalten*.

Öffnen Sie Ihren Dienst in einem Browser. Sie sehen, dass keine Meldung angezeigt wird. Wechseln Sie zurück zu Visual Studio Code. Sie sehen, dass Zeile 10 hervorgehoben ist. Durch den von Ihnen festgelegten Haltepunkt wurde der Dienst in Zeile 10 angehalten. Drücken Sie zum Fortsetzen des Diensts *F5*, oder klicken Sie auf *Debuggen* und dann auf *Weiter*. Wechseln Sie zurück zum Browser. Sie sehen, dass die Meldung jetzt angezeigt wird.

Beim Ausführen Ihres Diensts in Kubernetes mit einem angefügten Debugger haben Sie Vollzugriff auf Debuginformationen, z. B. Aufrufliste, lokale Variablen und Ausnahmeninformationen.

Entfernen Sie den Haltepunkt, indem Sie Ihren Cursor in `server.js` in Zeile 10 platzieren und *F9* drücken.

Klicken Sie auf *Debuggen* und dann auf *Debuggen beenden*, um den Debugger zu beenden.

## <a name="update-code-from-visual-studio-code"></a>Aktualisieren von Code aus Visual Studio Code

Ändern Sie den Debugmodus in *Attach to a Server (AZDS)* (An Server anfügen (AZDS)), und starten Sie den Dienst:

![](media/get-started-node/attach-nodejs.png)

Mit diesem Befehl wird Ihr Dienst in Azure Dev Spaces erstellt und ausgeführt. Außerdem wird im Container Ihres Diensts ein Prozess vom Typ [nodemon](https://nodemon.io) gestartet und VS Code angefügt. Der Prozess *nodemon* lässt automatische Neustarts zu, wenn Änderungen am Quellcode vorgenommen werden, um auf ähnliche Weise wie auf Ihrem lokalen Computer eine schnellere Entwicklung von inneren Schleifen zu ermöglichen.

Navigieren Sie nach dem Starten des Diensts über Ihren Browser dorthin, und interagieren Sie damit.

Wechseln Sie während der Ausführung des Diensts zu VS Code zurück, und aktualisieren Sie Zeile 10 in `server.js`. Beispiel:
```javascript
    res.send('Hello from webfrontend in Azure while debugging!');
```

Speichern Sie die Datei, und kehren Sie über einen Browser zu Ihrem Dienst zurück. Interagieren Sie mit dem Dienst, und achten Sie auf die Anzeige Ihrer aktualisierten Meldung.

Während der Ausführung von *nodemon* wird der Node-Prozess automatisch neu gestartet, sobald Codeänderungen erkannt werden. Dieser Prozess des automatischen Neustarts ähnelt dem Bearbeiten und Neustarten Ihres Diensts auf Ihrem lokalen Computer, um eine Umgebung für die Entwicklung von inneren Schleifen bereitzustellen.

## <a name="clean-up-your-azure-resources"></a>Bereinigen Ihrer Azure-Ressourcen

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich darüber, wie Azure Dev Spaces Sie bei der Entwicklung komplexerer containerübergreifender Anwendungen unterstützt und wie Sie die gemeinsame Entwicklung vereinfachen können, indem Sie in verschiedenen Bereichen mit verschiedenen Versionen oder Branches Ihres Codes arbeiten.

> [!div class="nextstepaction"]
> [Arbeiten mit mehreren Containern und Teamentwicklung](multi-service-nodejs.md)


[supported-regions]: about.md#supported-regions-and-configurations