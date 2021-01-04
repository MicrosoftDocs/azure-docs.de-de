---
title: 'Tutorial: Schnelle Containerimageerstellung'
description: In diesem Tutorial erfahren Sie, wie Sie mit Azure Container Registry Tasks (ACR Tasks) ein Docker-Containerimage in Azure erstellen und anschließend in Azure Container Instances bereitstellen.
ms.topic: tutorial
ms.date: 11/24/2020
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: b218f47348d5a26297f14c4bc788a6cf6b78cc60
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030327"
---
# <a name="tutorial-build-and-deploy-container-images-in-the-cloud-with-azure-container-registry-tasks"></a>Tutorial: Erstellen und Bereitstellen von Containerimages in der Cloud mit Azure Container Registry Tasks

[ACR Tasks](container-registry-tasks-overview.md) ist eine Suite mit Features in Azure Container Registry, die optimierte und effiziente Docker-Containerimage-Builds in Azure bietet. In diesem Artikel erfahren Sie, wie Sie das *Schnellaufgabenfeature* von ACR Tasks verwenden.

Der Entwicklungszyklus „Innere Schleife“ ist der iterative Prozess, der das Schreiben von Code sowie das Erstellen und Testen Ihrer Anwendung umfasst, bevor sie zur Quellcodeverwaltung committet wird. Eine Schnellaufgabe erweitert Ihre innere Schleife auf die Cloud und ermöglicht die Überprüfung des Erfolgsstatus von Buildvorgängen sowie automatisches Pushen erfolgreich erstellter Images an Ihre Containerregistrierung. Ihre Images werden nativ in der Cloud (in der Nähe Ihrer Registrierung) erstellt, was eine schnellere Bereitstellung ermöglicht.

Sämtliche Dockerfile-Kenntnisse lassen sich 1:1 auf ACR Tasks übertragen. Für die Erstellung in der Cloud mit ACR Tasks müssen Sie lediglich den ausgeführten Befehl ändern. Ihre Dockerfile-Dateien können Sie unverändert weiter verwenden. 

Dieser erste Teil einer Tutorialreihe umfasst Folgendes:

> [!div class="checklist"]
> * Abrufen des Quellcodes für die Beispielanwendung
> * Erstellen eines Containerimages in Azure
> * Bereitstellen eines Containers für Azure Container Instances

In den weiteren Tutorials erfahren Sie, wie Sie mithilfe von Tasks Containerimage-Buildvorgänge automatisieren, wenn Code committet oder ein Basisimage aktualisiert wird. ACR Tasks kann mithilfe einer YAML-Datei, in der Schritte zum Erstellen, Pushen und optional zum Testen mehrerer Container definiert werden, auch [Aufgaben mit mehreren Schritten](container-registry-tasks-multi-step.md) ausführen.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="github-account"></a>GitHub-Konto

Erstellen Sie unter https://github.com ein Konto, falls Sie noch keins besitzen. In dieser Tutorialreihe werden automatisierte Imagebuildvorgänge in ACR Tasks anhand eines GitHub-Repositorys veranschaulicht.

### <a name="fork-sample-repository"></a>Forken des Beispielrepositorys

Forken Sie als Nächstes das Beispielrepository über die Benutzeroberfläche von GitHub in Ihr GitHub-Konto. In diesem Tutorial erstellen Sie ein Containerimage auf der Grundlage der Quelle im Repository. Im nächsten Tutorial pushen Sie dann ein Commit an Ihr Fork des Repositorys, um eine automatische Aufgabe zu initiieren.

Forken Sie das folgende Repository: https://github.com/Azure-Samples/acr-build-helloworld-node

![Screenshot der Schaltfläche zum Forken (hervorgehoben) in GitHub][quick-build-01-fork]

### <a name="clone-your-fork"></a>Klonen Ihres Forks

Klonen Sie nach dem Forken des Repositorys Ihren Fork, und geben Sie das Verzeichnis ein, das Ihren lokalen Klon enthält.

Klonen Sie das Repository mit `git`, und ersetzen Sie dabei **\<your-github-username\>** durch Ihren GitHub-Benutzernamen:

```console
git clone https://github.com/<your-github-username>/acr-build-helloworld-node
```

Geben Sie das Verzeichnis ein, das den Quellcode enthält:

```console
cd acr-build-helloworld-node
```

### <a name="bash-shell"></a>Bash-Shell

Die Befehle in dieser Tutorialreihe sind für die Bash-Shell formatiert. Falls Sie lieber PowerShell, die Befehlszeile oder eine andere Shell verwenden möchten, müssen Sie ggf. die Zeilenfortsetzung und das Umgebungsvariablenformat entsprechend anpassen.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="build-in-azure-with-acr-tasks"></a>Erstellen in Azure mit ACR Tasks

Nachdem Sie den Quellcode auf Ihren Computer heruntergeladen haben, können Sie mit den folgenden Schritten eine Containerregistrierung und anschließend das Containerimage mit ACR Tasks erstellen.

In den Tutorials dieser Reihe werden Shell-Umgebungsvariablen verwendet, um das Ausführen der Beispielbefehle zu vereinfachen. Führen Sie den folgenden Befehl aus, um die Variable `ACR_NAME` festzulegen. Ersetzen Sie **\<registry-name\>** durch einen eindeutigen Namen für Ihre neue Containerregistrierung. Der Registrierungsname muss innerhalb von Azure eindeutig sein, darf nur Kleinbuchstaben enthalten und muss aus zwischen 5 und 50 alphanumerischen Zeichen bestehen. Da die anderen Ressourcen, die Sie in diesem Tutorial erstellen, auf diesem Namen basieren, sollte die Änderung dieser ersten Variablen ausreichend sein.

```console
ACR_NAME=<registry-name>
```

Nach Angabe der Umgebungsvariablen für die Containerregistrierung können Sie die restlichen Befehle aus diesem Tutorial ohne weitere Bearbeitung kopieren und einfügen. Führen Sie die folgenden Befehle aus, um eine Ressourcengruppe und eine Containerregistrierung zu erstellen:

```azurecli
RES_GROUP=$ACR_NAME # Resource Group name

az group create --resource-group $RES_GROUP --location eastus
az acr create --resource-group $RES_GROUP --name $ACR_NAME --sku Standard --location eastus
```

Erstellen Sie nach der Registrierungserstellung mithilfe von ACR Tasks ein auf dem Beispielcode basierendes Containerimage. Führen Sie den Befehl [az acr build][az-acr-build] aus, um eine *Schnellaufgabe* durchzuführen:

```azurecli
az acr build --registry $ACR_NAME --image helloacrtasks:v1 .
```

Die Ausgabe des Befehls [az acr build][az-acr-build] sollte in etwa wie im folgenden Beispiel aussehen. Sie sehen den Upload des Quellcodes („context“) in Azure sowie die Details des `docker build`-Vorgangs, den die ACR-Aufgabe in der Cloud ausführt. Da ACR-Aufgaben Ihre Images mithilfe von `docker build` erstellen, sind keine Änderungen an Ihren Dockerfiles-Dateien erforderlich, und Sie können sofort mit der Verwendung von ACR Tasks beginnen.

```output
Packing source code into tar file to upload...
Sending build context (4.813 KiB) to ACR...
Queued a build with build ID: da1
Waiting for build agent...
2020/11/18 18:31:42 Using acb_vol_01185991-be5f-42f0-9403-a36bb997ff35 as the home volume
2020/11/18 18:31:42 Setting up Docker configuration...
2020/11/18 18:31:43 Successfully set up Docker configuration
2020/11/18 18:31:43 Logging in to registry: myregistry.azurecr.io
2020/11/18 18:31:55 Successfully logged in
Sending build context to Docker daemon   21.5kB
Step 1/5 : FROM node:15-alpine
15-alpine: Pulling from library/node
Digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
Status: Image is up to date for node:15-alpine
 ---> a56170f59699
Step 2/5 : COPY . /src
 ---> 88087d7e709a
Step 3/5 : RUN cd /src && npm install
 ---> Running in e80e1263ce9a
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN helloworld@1.0.0 No repository field.

up to date in 0.1s
Removing intermediate container e80e1263ce9a
 ---> 26aac291c02e
Step 4/5 : EXPOSE 80
 ---> Running in 318fb4c124ac
Removing intermediate container 318fb4c124ac
 ---> 113e157d0d5a
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in fe7027a11787
Removing intermediate container fe7027a11787
 ---> 20a27b90eb29
Successfully built 20a27b90eb29
Successfully tagged myregistry.azurecr.io/helloacrtasks:v1
2020/11/18 18:32:11 Pushing image: myregistry.azurecr.io/helloacrtasks:v1, attempt 1
The push refers to repository [myregistry.azurecr.io/helloacrtasks]
6428a18b7034: Preparing
c44b9827df52: Preparing
172ed8ca5e43: Preparing
8c9992f4e5dd: Preparing
8dfad2055603: Preparing
c44b9827df52: Pushed
172ed8ca5e43: Pushed
8dfad2055603: Pushed
6428a18b7034: Pushed
8c9992f4e5dd: Pushed
v1: digest: sha256:b038dcaa72b2889f56deaff7fa675f58c7c666041584f706c783a3958c4ac8d1 size: 1366
2020/11/18 18:32:43 Successfully pushed image: myregistry.azurecr.io/helloacrtasks:v1
2020/11/18 18:32:43 Step ID acb_step_0 marked as successful (elapsed time in seconds: 15.648945)
The following dependencies were found:
- image:
    registry: myregistry.azurecr.io
    repository: helloacrtasks
    tag: v1
    digest: sha256:b038dcaa72b2889f56deaff7fa675f58c7c666041584f706c783a3958c4ac8d1
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git: {}

Run ID: da1 was successful after 1m9.970148252s
```

Gegen Ende der Ausgabe zeigt ACR Tasks die für Ihr Image ermittelten Abhängigkeiten an. Dies ermöglicht ACR Tasks die Automatisierung von Imagebuildvorgängen nach einer Basisimageaktualisierung – etwa, wenn ein Basisimage mit Betriebssystem- oder Frameworkpatches aktualisiert wird. Auf die Unterstützung von Basisimageaktualisierungen durch ACR Tasks wird im weiteren Verlauf dieser Tutorialreihe noch näher eingegangen.

## <a name="deploy-to-azure-container-instances"></a>Bereitstellen in Azure Container Instances

Erfolgreich erstellte Images werden von ACR Tasks standardmäßig automatisch an Ihre Registrierung gepusht, von wo aus sie sofort bereitgestellt werden können.

In diesem Abschnitt erstellen Sie eine Azure Key Vault-Instanz und einen Dienstprinzipal und stellen anschließend den Container unter Verwendung der Anmeldeinformationen des Dienstprinzipals in Azure Container Instances (ACI) bereit.

### <a name="configure-registry-authentication"></a>Konfigurieren der Authentifizierung der Registrierung

In Produktionsszenarien müssen für den Zugriff auf eine Azure-Containerregistrierung immer [Dienstprinzipale][service-principal-auth] verwendet werden. Dienstprinzipale ermöglichen Ihnen eine rollenbasierte Zugriffssteuerung auf Ihre Containerimages. Beispielsweise können Sie einen Dienstprinzipal mit ausschließlichem Pullzugriff auf eine Registrierung konfigurieren.

#### <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors

Wenn Sie noch keinen Tresor in [Azure Key Vault](../key-vault/index.yml) haben, erstellen Sie einen mithilfe der Azure CLI und folgenden Befehle.

```azurecli
AKV_NAME=$ACR_NAME-vault

az keyvault create --resource-group $RES_GROUP --name $AKV_NAME
```

#### <a name="create-a-service-principal-and-store-credentials"></a>Erstellen eines Dienstprinzipals und Speichern von Anmeldeinformationen

Sie müssen nun einen Dienstprinzipal erstellen und seine Anmeldeinformationen in Ihrem Schlüsselspeicher speichern.

Erstellen Sie mithilfe des Befehls [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] den Dienstprinzipal, und speichern Sie das Kennwort (**password**) des Dienstprinzipals mithilfe des Befehls [az keyvault secret set][az-keyvault-secret-set] im Tresor:

```azurecli
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

Das Argument `--role` im vorhergehenden Befehl konfiguriert den Dienstprinzipal mit der Rolle *acrpull*, die ihm ausschließlich Pullzugriff auf die Registrierung gewährt. Um sowohl Push- als auch Pullzugriff zu gewähren, ändern Sie das Argument `--role` in *acrpush*.

Speichern Sie als Nächstes die App-ID (*appId*) des Dienstprinzipals im Tresor. Dies ist der Benutzername (**username**), den Sie zur Authentifizierung an Azure Container Registry übergeben:

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

### <a name="deploy-a-container-with-azure-cli"></a>Bereitstellen eines Containers mit der Azure CLI

Nachdem die Anmeldeinformationen des Dienstprinzipals als Azure Key Vault-Geheimnisse gespeichert wurden, können sie von Ihren Anwendungen und Diensten für den Zugriff auf Ihre private Registrierung verwendet werden.

Führen Sie den folgenden [az container create][az-container-create]-Befehl aus, um eine Containerinstanz bereitzustellen. Der Befehl verwendet die in Azure Key Vault gespeicherten Anmeldeinformationen des Dienstprinzipals, um sich bei Ihrer Containerregistrierung zu authentifizieren.

```azurecli
az container create \
    --resource-group $RES_GROUP \
    --name acr-tasks \
    --image $ACR_NAME.azurecr.io/helloacrtasks:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label acr-tasks-$ACR_NAME \
    --query "{FQDN:ipAddress.fqdn}" \
    --output table
```

Da der Wert `--dns-name-label` innerhalb von Azure eindeutig sein muss, fügt der vorherige Befehl den Namen Ihrer Containerregistrierung an die DNS-Namensbezeichnung des Containers an. Die Ausgabe des Befehls zeigt den vollqualifizierten Domänennamen (FQDN) des Containers. Beispiel:

```output
FQDN
----------------------------------------------
acr-tasks-myregistry.eastus.azurecontainer.io
```

Notieren Sie sich den FQDN des Containers. Er wird im nächsten Abschnitt benötigt.

### <a name="verify-the-deployment"></a>Überprüfen der Bereitstellung

Verwenden Sie den Befehl [az container attach][az-container-attach], um den Startprozess des Containers zu überwachen:

```azurecli
az container attach --resource-group $RES_GROUP --name acr-tasks
```

Die Ausgabe von `az container attach` zeigt zunächst den Status des Containers an, während das Image gepullt und gestartet wird, und bindet dann die Ausgaben „STDOUT“ und „STDERR“ Ihrer lokalen Konsole an die des Containers.

```output
Container 'acr-tasks' is in state 'Running'...
(count: 1) (last timestamp: 2020-11-18 18:39:10+00:00) pulling image "myregistry.azurecr.io/helloacrtasks:v1"
(count: 1) (last timestamp: 2020-11-18 18:39:15+00:00) Successfully pulled image "myregistry.azurecr.io/helloacrtasks:v1"
(count: 1) (last timestamp: 2020-11-18 18:39:17+00:00) Created container
(count: 1) (last timestamp: 2020-11-18 18:39:17+00:00) Started container

Start streaming logs:
Server running at http://localhost:80
```

Wenn `Server running at http://localhost:80` angezeigt wird, navigieren Sie in Ihrem Browser zum FQDN des Containers, um die ausgeführte Anwendung anzuzeigen. Der FQDN sollte in der Ausgabe des `az container create`-Befehls angezeigt worden sein, den Sie im vorherigen Abschnitt ausgeführt haben.

:::image type="content" source="media/container-registry-tutorial-quick-build/quick-build-02-browser.png" alt-text="Im Browser ausgeführte Beispielanwendung":::

Drücken Sie `Control+C`, um Ihre Konsole vom Container zu trennen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Beenden Sie die Containerinstanz mithilfe des Befehls [az container delete][az-container-delete]:

```azurecli
az container delete --resource-group $RES_GROUP --name acr-tasks
```

Führen Sie die folgenden Befehle aus, um *alle* Ressourcen zu entfernen, die Sie im Rahmen dieses Tutorials erstellt haben (einschließlich der Containerregistrierung, des Schlüsseltresors und des Dienstprinzipals). Falls Sie allerdings direkt mit dem [nächsten Tutorial](container-registry-tutorial-build-task.md) fortfahren möchten, empfiehlt es sich, die Ressourcen nicht zu bereinigen, da dort die gleichen Ressourcen benötigt werden.

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre innere Schleife mit einer Schnellaufgabe getestet haben, können Sie eine **Buildaufgabe** konfigurieren, die dafür sorgt, dass ein Buildvorgang für Containerimages ausgelöst wird, wenn Sie Quellcode in einem Git-Repository committen:

> [!div class="nextstepaction"]
> [Auslösen automatischer Builds mithilfe von Aufgaben](container-registry-tutorial-build-task.md)

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-keyvault-create]: /cli/azure/keyvault/secret#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-login]: /cli/azure/reference-index#az-login
[service-principal-auth]: container-registry-auth-service-principal.md

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
