---
title: Tasks-Workflow zum Verwalten von öffentlichen Registrierungsinhalten
description: Erstellen Sie einen automatisierten Azure Container Registry Tasks-Workflow, um die Inhalte öffentlicher Images in einer privaten Azure Container Registry-Instanz nachzuverfolgen, zu verwalten und zu verwenden.
author: SteveLasker
ms.topic: article
ms.author: stevelas
ms.date: 10/29/2020
ms.custom: ''
ms.openlocfilehash: 4fba6290b4973e797c13943fc9be4fadb19f3274
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349281"
---
# <a name="how-to-consume-and-maintain-public-content-with-azure-container-registry-tasks"></a>Verwenden und Verwalten von öffentlichen Inhalten mit Azure Container Registry Tasks

Dieser Artikel enthält einen Beispielworkflow in Azure Container Registry, der Ihnen als Hilfe beim Verwenden und Verwalten von öffentlichen Inhalten dienen soll:

1. Importieren lokaler Kopien von abhängigen öffentlichen Images
1. Überprüfen von öffentlichen Images per Sicherheitsüberprüfung und mit Funktionstests
1. Höherstufen der Images in private Registrierungen für die interne Nutzung
1. Auslösen von Basisimageaktualisierungen für Anwendungen, die von öffentlichen Inhalten abhängig sind
1. Verwenden von [Azure Container Registry Tasks](container-registry-tasks-overview.md) zum Automatisieren des Workflows

Die folgende Abbildung enthält eine Übersicht über diesen Workflow:

![Nutzen von öffentlichen Inhalten: Workflow](./media/tasks-consume-public-content/consuming-public-content-workflow.png)

Mit dem Importworkflow mit Kontrollmechanismus (Gated Workflow) können Sie die Abhängigkeiten Ihrer Organisation auf extern verwalteten Artefakten verwalten. Beispiele hierfür sind Images, die aus öffentlichen Registrierungen stammen, z. B. [Docker Hub][docker-hub], [GCR][gcr], [Quay][quay], [GitHub Container Registry][ghcr], [Microsoft Container Registry][mcr] oder auch andere [Azure-Containerregistrierungen][acr]. 

Hintergrundinformationen zu den Risiken, die mit Abhängigkeiten von öffentlichen Inhalten verbunden sein können, und zur Verwendung von Azure Container Registry für die Risikominderung finden Sie im [Blogbeitrag zur Nutzung öffentlicher Inhalte durch OCI][oci-consuming-public-content] und unter [Verwalten öffentlicher Inhalte mit Azure Container Registry](buffer-gate-public-content.md).

Sie können für diese exemplarische Vorgehensweise Azure Cloud Shell oder eine lokale Installation der Azure CLI verwenden. Die Nutzung von Azure CLI-Version 2.10 oder höher wird empfohlen. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][install-cli].

## <a name="scenario-overview"></a>Übersicht über das Szenario

![Importieren von Workflowkomponenten](./media/tasks-consume-public-content/consuming-public-content-objects.png)

Bei dieser exemplarischen Vorgehensweise wird Folgendes eingerichtet:

1. Drei **Containerregistrierungen**, nämlich:
   * Ein simulierter [Docker Hub][docker-hub] (`publicregistry`), um die Änderung des Basisimages zu unterstützen
   * Teamregistrierung (`contoso`) zur gemeinsamen Nutzung von Images
   * Freigegebene Unternehmens-/Teamregistrierung (`baseartifacts`) für importierte öffentliche Inhalte
1. Ein **ACR-Task** in jeder Registrierung. Die Tasks dienen folgenden Zwecken:  
   1. Erstellen eines simulierten öffentlichen `node`-Images
   1. Importieren und Überprüfen des `node`-Images für die freigegebene Unternehmens-/Teamregistrierung
   1. Erstellen und Bereitstellen des `hello-world`-Images
1. **Definitionen des ACR-Tasks**, einschließlich Konfigurationen für:
1. Eine Sammlung mit **Anmeldeinformationen für die Registrierung**, bei denen es sich um Verweise auf einen Schlüsseltresor handelt
1. Eine Sammlung mit **Geheimnissen** in der Datei `acr-task.yaml`, bei denen es sich um Verweise auf einen Schlüsseltresor handelt
1. Eine Sammlung mit **konfigurierten Werten**, die in der Datei `acr-task.yaml` verwendet werden
1. Ein **Azure-Schlüsseltresor** als Schutz für alle Geheimnisse
1. Eine **Azure-Containerinstanz** zum Hosten der Buildanwendung `hello-world`

## <a name="prerequisites"></a>Voraussetzungen

In den folgenden Schritten werden Werte für Ressourcen konfiguriert, die in der exemplarischen Vorgehensweise erstellt und verwendet werden.

### <a name="set-environment-variables"></a>Festlegen von Umgebungsvariablen

Konfigurieren Sie Variablen, die für Ihre Umgebung eindeutig sind. Wir verwenden bewährte Methoden zum Platzieren von Ressourcen mit permanenten Inhalten jeweils in einer eigenen Ressourcengruppe, um die Anzahl von versehentlichen Löschvorgängen zu reduzieren. Sie können diese Ressourcen bei Bedarf aber auch in einer gemeinsamen Ressourcengruppe platzieren.

Die Beispiele in diesem Artikel sind für die Bash-Shell formatiert.

```bash
# Set the three registry names, must be globally unique:
REGISTRY_PUBLIC=publicregistry
REGISTRY_BASE_ARTIFACTS=contosobaseartifacts
REGISTRY=contoso

# set the location all resources will be created in:
RESOURCE_GROUP_LOCATION=eastus

# default resource groups
REGISTRY_PUBLIC_RG=${REGISTRY_PUBLIC}-rg
REGISTRY_BASE_ARTIFACTS_RG=${REGISTRY_BASE_ARTIFACTS}-rg
REGISTRY_RG=${REGISTRY}-rg

# fully qualified registry urls
REGISTRY_DOCKERHUB_URL=docker.io
REGISTRY_PUBLIC_URL=${REGISTRY_PUBLIC}.azurecr.io
REGISTRY_BASE_ARTIFACTS_URL=${REGISTRY_BASE_ARTIFACTS}.azurecr.io
REGISTRY_URL=${REGISTRY}.azurecr.io

# Azure key vault for storing secrets, name must be globally unique
AKV=acr-task-credentials
AKV_RG=${AKV}-rg

# ACI for hosting the deployed application
ACI=hello-world-aci
ACI_RG=${ACI}-rg
```

### <a name="git-repositories-and-tokens"></a>Git-Repositorys und -Token

Forken Sie zum Simulieren Ihrer Umgebung die folgenden Git-Repositorys jeweils in Repositorys, die Sie verwalten können. 

* https://github.com/importing-public-content/base-image-node.git
* https://github.com/importing-public-content/import-baseimage-node.git
* https://github.com/importing-public-content/hello-world.git

Aktualisieren Sie anschließend die folgenden Variablen für Ihre geforkten Repositorys.

Der Zusatz `:main` am Ende der Git-URLs steht für den Standardbranch des Repositorys.

```bash
GIT_BASE_IMAGE_NODE=https://github.com/<your-fork>/base-image-node.git#main
GIT_NODE_IMPORT=https://github.com/<your-fork>/import-baseimage-node.git#main
GIT_HELLO_WORLD=https://github.com/<your-fork>/hello-world.git#main
```

Sie benötigen ein [GitHub-Zugriffstoken][git-token] (persönliches Zugriffstoken, PAT) für ACR Tasks, um Git-Webhooks klonen und einrichten zu können. Schritte zum Erstellen eines Tokens mit den erforderlichen Berechtigungen für ein privates Repository finden Sie unter [Erstellen eines persönlichen GitHub-Zugriffstokens](container-registry-tutorial-build-task.md#create-a-github-personal-access-token). 

```bash
GIT_TOKEN=<set-git-token-here>
```

### <a name="docker-hub-credentials"></a>Docker Hub-Anmeldeinformationen  
Erstellen Sie ein [Docker Hub-Token][docker-hub-tokens], um beim Pullen von Images aus Docker Hub die Drosselung und Identitätsanforderungen zu vermeiden. Legen Sie anschließend die folgenden Umgebungsvariablen fest:

```bash
REGISTRY_DOCKERHUB_USER=<yourusername>
REGISTRY_DOCKERHUB_PASSWORD=<yourtoken>
```

### <a name="create-registries"></a>Erstellen von Registrierungen

Erstellen Sie mit Azure CLI-Befehlen drei Containerregistrierungen mit Premium-Tarif. Verwenden Sie für jede eine eigene Ressourcengruppe:

```azurecli-interactive
az group create --name $REGISTRY_PUBLIC_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_PUBLIC_RG --name $REGISTRY_PUBLIC --sku Premium

az group create --name $REGISTRY_BASE_ARTIFACTS_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_BASE_ARTIFACTS_RG --name $REGISTRY_BASE_ARTIFACTS --sku Premium

az group create --name $REGISTRY_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_RG --name $REGISTRY --sku Premium
```

### <a name="create-key-vault-and-set-secrets"></a>Erstellen eines Schlüsseltresors und Festlegen von Geheimnissen

Erstellen Sie einen Schlüsseltresor:

```azurecli-interactive
az group create --name $AKV_RG --location $RESOURCE_GROUP_LOCATION
az keyvault create --resource-group $AKV_RG --name $AKV
```

Legen Sie den Docker Hub-Benutzernamen und das Token im Schlüsseltresor fest:

```azurecli-interactive
az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-user \
--value $REGISTRY_DOCKERHUB_USER

az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-password \
--value $REGISTRY_DOCKERHUB_PASSWORD
```

Legen Sie im Schlüsseltresor ein persönliches Git-Zugriffstoken fest, und überprüfen Sie es:

```azurecli-interactive
az keyvault secret set --vault-name $AKV --name github-token --value $GIT_TOKEN

az keyvault secret show --vault-name $AKV --name github-token --query value -o tsv
```

### <a name="create-resource-group-for-an-azure-container-instance"></a>Erstellen einer Ressourcengruppe für eine Azure-Containerinstanz

Diese Ressourcengruppe wird in einem späteren Task zum Bereitstellen des `hello-world`-Images verwendet.

```azurecli-interactive
az group create --name $ACI_RG --location $RESOURCE_GROUP_LOCATION
```

## <a name="create-public-node-base-image"></a>Erstellen eines öffentlichen `node`-Basisimages

Erstellen Sie zum Simulieren des `node`-Images unter Docker Hub einen [ACR-Task][acr-task] für die Erstellung und Verwaltung des öffentlichen Images. Bei diesem Setup können Änderungen mit den Maintainern des `node`-Images simuliert werden.

```azurecli-interactive
az acr task create \
  --name node-public \
  -r $REGISTRY_PUBLIC \
  -f acr-task.yaml \
  --context $GIT_BASE_IMAGE_NODE \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_DOCKERHUB_URL}/ \
  --assign-identity
```

Fügen Sie dem Task zum Vermeiden der Docker-Drosselung [Docker Hub-Anmeldeinformationen][docker-hub-tokens] hinzu. Sie können den Befehl [acr task credentials][acr-task-credentials] verwenden, um Docker-Anmeldeinformationen an beliebige Registrierungen zu übergeben, z. B. Docker Hub.

```azurecli-interactive
az acr task credential add \
  -n node-public \
  -r $REGISTRY_PUBLIC \
  --login-server $REGISTRY_DOCKERHUB_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-dockerhub-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-dockerhub-password \
  --use-identity [system]
```

Gewähren Sie dem Task Zugriff zum Lesen von Werten aus dem Schlüsseltresor:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name node-public \
                  --registry $REGISTRY_PUBLIC \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

[Tasks können durch Git-Commits, Basisimageaktualisierungen, Timer oder manuelle Ausführungen ausgelöst werden][acr-task-triggers]. 

Führen Sie den Task manuell aus, um das `node`-Image zu generieren:

```azurecli-interactive
az acr task run -r $REGISTRY_PUBLIC -n node-public
```

Listen Sie das Image in der simulierten öffentlichen Registrierung auf:

```azurecli-interactive
az acr repository show-tags -n $REGISTRY_PUBLIC --repository node
```

## <a name="create-the-hello-world-image"></a>Erstellen des `hello-world`-Images

Erstellen Sie basierend auf dem simulierten öffentlichen `node`-Image ein `hello-world`-Image.

### <a name="create-token-for-pull-access-to-simulated-public-registry"></a>Erstellen eines Tokens für Pullzugriff auf die simulierte öffentliche Registrierung

Erstellen Sie ein [Zugriffstoken][acr-tokens] für die simulierte öffentliche Registrierung mit dem Bereich `pull`. Legen Sie es anschließend im Schlüsseltresor fest:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-user" \
  --value "registry-${REGISTRY_PUBLIC}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_PUBLIC}-user" \
              --registry $REGISTRY_PUBLIC \
              --scope-map _repositories_pull \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-token-for-pull-access-by-azure-container-instances"></a>Erstellen eines Tokens für Pullzugriff durch Azure Container Instances

Erstellen Sie für den Bereich „pull“ ein [Zugriffstoken][acr-tokens] für die Registrierung, von der das `hello-world`-Image gehostet wird. Legen Sie es anschließend im Schlüsseltresor fest:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-user" \
  --value "registry-${REGISTRY}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY}-user" \
              --registry $REGISTRY \
              --repository hello-world content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-task-to-build-and-maintain-hello-world-image"></a>Erstellen eines Tasks zum Erstellen und Verwalten des `hello-world`-Images

Mit dem folgenden Befehl wird ein Task aus der Definition in `acr-tasks.yaml` im `hello-world` Repository erstellt. Mit den Taskschritten wird das `hello-world`-Image erstellt und dann in Azure Container Instances bereitgestellt. Die Ressourcengruppe für Azure Container Instances wurde in einem vorherigen Abschnitt erstellt. Indem Sie `az container create` im Task nur mit einer Abweichung bei `image:tag` aufrufen, wird der Task für diese gesamte exemplarische Vorgehensweise für dieselbe Instanz bereitgestellt.

```azurecli-interactive
az acr task create \
  -n hello-world \
  -r $REGISTRY \
  -f acr-task.yaml \
  --context $GIT_HELLO_WORLD \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --set KEYVAULT=$AKV \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG \
  --assign-identity
```

Fügen Sie dem Task Anmeldeinformationen für die simulierte öffentliche Registrierung hinzu:

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

Gewähren Sie dem Task Zugriff zum Lesen von Werten aus dem Schlüsseltresor:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name hello-world \
                  --registry $REGISTRY \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

Erteilen Sie dem Task Zugriff auf die Erstellung und Verwaltung von Azure Container Instances, indem Sie Zugriff auf die Ressourcengruppe gewähren:

```azurecli-interactive
az role assignment create \
  --assignee $(az acr task show \
  --name hello-world \
  --registry $REGISTRY \
  --query identity.principalId --output tsv) \
  --scope $(az group show -n $ACI_RG --query id -o tsv) \
  --role owner
```

Führen Sie den Task nach dem Erstellen und Konfigurieren aus, um das `hello-world`-Image zu erstellen und bereitzustellen:

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

Rufen Sie nach der Erstellung die IP-Adresse des Containers ab, von dem das `hello-world`-Image gehostet wird.

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

Navigieren Sie in Ihrem Browser zur IP-Adresse, um die ausgeführte Anwendung anzuzeigen.

## <a name="update-the-base-image-with-a-questionable-change"></a>Aktualisieren des Basisimages mit einer „fragwürdigen“ Änderung

In diesem Abschnitt wird eine Änderung des Basisimages simuliert, die in der Umgebung zu Problemen führen kann.

1. Öffnen Sie den `Dockerfile` im geforkten Repository `base-image-node`.
1. Ändern Sie `BACKGROUND_COLOR` in `Orange`, um die Änderung zu simulieren.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Orange
```

Committen Sie die Änderung, und verfolgen Sie, wie ACR Tasks automatisch mit der Erstellung beginnt.

Zeigen Sie die Ausführung des Tasks an:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

Basierend auf dem TRIGGER `Commit` sollte unter STATUS der Status `Succeeded` angezeigt werden:

```azurecli-interactive
RUN ID    TASK      PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------  ----------  ---------  ---------  --------------------  ----------
ca4       hub-node  linux       Succeeded  Commit     2020-10-24T05:02:29Z  00:00:22
```

Drücken Sie **STRG+C**, um den watch-Befehl zu beenden, und zeigen Sie anschließend die Protokolle für die letzte Ausführung an:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

Nach Abschluss des Vorgangs für das `node`-Image führen Sie `watch` für ACR Tasks aus, damit automatisch mit dem Erstellen des `hello-world`-Images begonnen wird:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

Basierend auf dem TRIGGER `Image Update` sollte unter STATUS der Status `Succeeded` angezeigt werden:

```azurecli-interactive
RUN ID    TASK         PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  -----------  ----------  ---------  ------------  --------------------  ----------
dau       hello-world  linux       Succeeded  Image Update  2020-10-24T05:08:45Z  00:00:31
```

Drücken Sie **STRG+C**, um den watch-Befehl zu beenden, und zeigen Sie anschließend die Protokolle für die letzte Ausführung an:

```azurecli-interactive
az acr task logs -r $REGISTRY
```

Rufen Sie nach Abschluss des Vorgangs die IP-Adresse der Site ab, von der das aktualisierte `hello-world`-Image gehostet wird:

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

Navigieren Sie in Ihrem Browser zu der Site, die den orangefarbenen (fragwürdigen) Hintergrund aufweist.

### <a name="checking-in"></a>Einchecken

Sie haben nun ein `hello-world`-Image erstellt, für das basierend auf Git-Commits und Änderungen des `node`-Basisimages automatisch der Buildvorgang erfolgt. In diesem Beispiel wird für den Buildvorgang des Tasks ein Basisimage in Azure Container Registry verwendet, aber Sie können auch eine beliebige andere Registrierung nutzen.

Bei der Basisimageaktualisierung wird die Taskausführung automatisch erneut ausgelöst, wenn das `node`-Image aktualisiert wird. Wie hier zu sehen ist, sind nicht alle Aktualisierungen gewünscht.

## <a name="gated-imports-of-public-content"></a>Importe öffentlicher Inhalte mit Kontrollmechanismus

Um zu verhindern, dass Upstream-Änderungen zu einer Beschädigung kritischer Workloads führen, können eine Sicherheitsüberprüfung und Funktionstests hinzugefügt werden.

In diesem Abschnitt erstellen Sie einen ACR-Task für folgende Zwecke:

* Erstellen eines Testimages
* Ausführen des Funktionstestskripts `./test.sh` für das Testimage
* Importieren des öffentlichen Images in die Registrierung **baseimages**, wenn der Test des Images erfolgreich war

### <a name="add-automation-testing"></a>Hinzufügen eines Automatisierungstests

Es wird ein automatisierter Test implementiert, um Upstream-Inhalte mit einem Kontrollmechanismus zu versehen. In diesem Beispiel wird die Datei `test.sh` bereitgestellt, um `$BACKGROUND_COLOR` zu überprüfen. Wenn der Test nicht erfolgreich ist, wird `1` als `EXIT_CODE` zurückgegeben. Dies führt zu einem Fehler für den Schritt des ACR-Tasks, und die Ausführung wird beendet. Die Tests können in allen Tools erweitert werden, einschließlich der Ergebnisprotokollierung. Der Kontrollmechanismus wird im Skript mit einer Antwort vom Typ „Bestanden/Nicht bestanden“ verwaltet. Dies ist hier dargestellt:

```bash
if [ ""$(echo $BACKGROUND_COLOR | tr '[:lower:]' '[:upper:]') = 'RED' ]; then
    echo -e "\e[31mERROR: Invalid Color:\e[0m" ${BACKGROUND_COLOR}
    EXIT_CODE=1
else
  echo -e "\e[32mValidation Complete - No Known Errors\e[0m"
fi
exit ${EXIT_CODE}
```
### <a name="task-yaml"></a>YAML-Code für Task 

Überprüfen Sie im Repository `import-baseimage-node` die Datei `acr-task.yaml`, mit der die folgenden Schritte ausgeführt werden:

1. Erstellen Sie das Testbasisimage mit dem folgenden Dockerfile:
    ```dockerfile
    ARG REGISTRY_FROM_URL=
    FROM ${REGISTRY_FROM_URL}node:15-alpine
    WORKDIR /test
    COPY ./test.sh .
    CMD ./test.sh
    ```
1. Überprüfen Sie nach Abschluss des Vorgangs das Image, indem Sie den Container ausführen, mit dem `./test.sh` ausgeführt wird.
1. Führen Sie nur nach dem erfolgreichen Abschluss des Vorgangs die Schritte für den Import aus, für die die Kontrolle `when: ['validate-base-image']` verwendet wird.

```yaml
version: v1.1.0
steps:
  - id: build-test-base-image
    # Build off the base image we'll track
    # Add a test script to do unit test validations
    # Note: the test validation image isn't saved to the registry
    # but the task logs captures log validation results
    build: >
      --build-arg REGISTRY_FROM_URL={{.Values.REGISTRY_FROM_URL}}
      -f ./Dockerfile
      -t {{.Run.Registry}}/node-import:test
      .
  - id: validate-base-image
    # only continues if node-import:test returns a non-zero code
    when: ['build-test-base-image']
    cmd: "{{.Run.Registry}}/node-import:test"
  - id: pull-base-image
    # import the public image to base-artifacts
    # Override the stable tag,
    # and create a unique tag to enable rollback
    # to a previously working image
    when: ['validate-base-image']
    cmd: >
        docker pull {{.Values.REGISTRY_FROM_URL}}node:15-alpine
  - id: retag-base-image
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine
  - id: retag-base-image-unique-tag
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine-{{.Run.ID}}
  - id: push-base-image
    when: ['retag-base-image', 'retag-base-image-unique-tag']
    push:
    - "{{.Run.Registry}}/node:15-alpine"
    - "{{.Run.Registry}}/node:15-alpine-{{.Run.ID}}"
```

### <a name="create-task-to-import-and-test-base-image"></a>Erstellen eines Tasks zum Importieren und Testen des Basisimages

```azurecli-interactive
  az acr task create \
  --name base-import-node \
  -f acr-task.yaml \
  -r $REGISTRY_BASE_ARTIFACTS \
  --context $GIT_NODE_IMPORT \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --assign-identity
```

Fügen Sie dem Task Anmeldeinformationen für die simulierte öffentliche Registrierung hinzu:

```azurecli-interactive
az acr task credential add \
  -n base-import-node \
  -r $REGISTRY_BASE_ARTIFACTS \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

Gewähren Sie dem Task Zugriff zum Lesen von Werten aus dem Schlüsseltresor:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name base-import-node \
                  --registry $REGISTRY_BASE_ARTIFACTS \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

Führen Sie den Importtask aus:

```azurecli-interactive
az acr task run -n base-import-node -r $REGISTRY_BASE_ARTIFACTS
```

> [!NOTE]
> Falls für den Task der Fehler `./test.sh: Permission denied` auftritt, sollten Sie sicherstellen, dass das Skript über Ausführungsberechtigungen verfügt, und einen Commit zurück in das Git-Repository durchführen:
>```bash
>chmod +x ./test.sh
>```

## <a name="update-hello-world-image-to-build-from-gated-node-image"></a>Aktualisieren des `hello-world`-Images auf die Erstellung aus dem kontrollierten `node`-Image

Erstellen Sie ein [Zugriffstoken][acr-tokens], um über das Repository `node` auf die Registrierung mit den Basisartefakten mit dem Bereich `read` zuzugreifen. Legen Sie anschließend im Schlüsseltresor Folgendes fest:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
  --value "registry-${REGISTRY_BASE_ARTIFACTS}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
              --registry $REGISTRY_BASE_ARTIFACTS \
              --repository node content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

Fügen Sie dem Task **hello-world** Anmeldeinformationen für die Registrierung mit den Basisartefakten hinzu:

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_BASE_ARTIFACTS_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-password \
  --use-identity [system]
```

Aktualisieren Sie den Task, um die `REGISTRY_FROM_URL` so zu ändern, dass die Registrierung `BASE_ARTIFACTS` verwendet wird:

```azurecli-interactive
az acr task update \
  -n hello-world \
  -r $REGISTRY \
  --set KEYVAULT=$AKV \
  --set REGISTRY_FROM_URL=${REGISTRY_BASE_ARTIFACTS_URL}/ \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG
```

Führen Sie den Task **hello-world** aus, um die zugehörige Abhängigkeit vom Basisimage zu ändern:

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

## <a name="update-the-base-image-with-a-valid-change"></a>Aktualisieren des Basisimages mit einer „gültigen“ Änderung

1. Öffnen Sie den `Dockerfile` im Repository `base-image-node`.
1. Ändern Sie `BACKGROUND_COLOR` in `Green`, um eine gültige Änderung zu simulieren.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Green
```

Committen Sie die Änderung, und überwachen Sie die Updatesequenz:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

Drücken Sie nach dem Beginn der Ausführung **STRG+C**, und überwachen Sie die Protokolle:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

Überwachen Sie nach Abschluss des Vorgangs den Task **base-image-import**:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

Drücken Sie nach dem Beginn der Ausführung **STRG+C**, und überwachen Sie die Protokolle:

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

Überwachen Sie nach Abschluss des Vorgangs den Task **hello-world**:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

Drücken Sie nach dem Beginn der Ausführung **STRG+C**, und überwachen Sie die Protokolle:

```azurecli-interactive
az acr task logs -r $REGISTRY
```

Rufen Sie nach Abschluss des Vorgangs die IP-Adresse der Site ab, von der das aktualisierte `hello-world`-Image gehostet wird:

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

Navigieren Sie in Ihrem Browser zu der Site, die nun einen grünen (gültigen) Hintergrund aufweisen sollte.

### <a name="view-the-gated-workflow"></a>Anzeigen des Workflows mit Kontrollmechanismus

Führen Sie die Schritte des vorherigen Abschnitts erneut aus, indem Sie die Hintergrundfarbe Rot verwenden.

1. Öffnen Sie den `Dockerfile` im Repository `base-image-node`.
1. Ändern Sie `BACKGROUND_COLOR` in `Red`, um eine ungültige Änderung zu simulieren.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Red
```

Committen Sie die Änderung, und überwachen Sie die Updatesequenz:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

Drücken Sie nach dem Beginn der Ausführung **STRG+C**, und überwachen Sie die Protokolle:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

Überwachen Sie nach Abschluss des Vorgangs den Task **base-image-import**:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

Drücken Sie nach dem Beginn der Ausführung **STRG+C**, und überwachen Sie die Protokolle:

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

Sie sollten nun verfolgen können, dass der Task **base-import-node** die Überprüfung nicht besteht und die Sequenz beendet wird, um ein `hello-world`-Update veröffentlichen zu können. Die Ausgabe Die Ausgabe lautet in etwa wie folgt:

```console
[...]
2020/10/30 03:57:39 Launching container with name: validate-base-image
Validating Image
NODE_VERSION: 15-alpine
BACKGROUND_COLOR: Red
ERROR: Invalid Color: Red
2020/10/30 03:57:40 Container failed during run: validate-base-image. No retries remaining.
failed to run step ID: validate-base-image: exit status 1
```

### <a name="publish-an-update-to-hello-world"></a>Veröffentlichen eines Updates für `hello-world`

Die Änderungen am `hello-world`-Image werden fortgesetzt, indem das letzte überprüfte `node`-Image verwendet wird.

Für alle weiteren Änderungen am `node`-Basisimage, die die Überprüfungen des Kontrollmechanismus bestehen, werden Basisimageaktualisierungen für das `hello-world`-Image ausgelöst.

## <a name="cleaning-up"></a>Bereinigung

Löschen Sie die in diesem Artikel verwendeten Ressourcen, falls Sie sie nicht mehr benötigen.

```azurecli-interactive
az group delete -n $REGISTRY_RG --no-wait -y
az group delete -n $REGISTRY_PUBLIC_RG --no-wait -y
az group delete -n $REGISTRY_BASE_ARTIFACTS_RG --no-wait -y
az group delete -n $AKV_RG --no-wait -y
az group delete -n $ACI_RG --no-wait -y
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie ACR-Tasks verwendet, um einen automatisierten Workflow mit Kontrollmechanismus zu erstellen, bei dem aktualisierte Basisimages in Ihre Umgebung eingefügt werden. Sehen Sie sich die hierzu passenden Informationen zur Verwaltung von Images in Azure Container Registry an.


* [Empfehlungen für das Tagging und die Versionsverwaltung von Containerimages](container-registry-image-tag-version.md)
* [Sperren von Containerimages in einer Azure-Containerregistrierung](container-registry-image-lock.md)

[install-cli]:                  /cli/azure/install-azure-cli
[acr]:                          https://aka.ms/acr
[acr-repo-permissions]:         ./container-registry-repository-scoped-permissions.md
[acr-task]:                     ./container-registry-tasks-overview.md
[acr-task-triggers]:            container-registry-tasks-overview.md#task-scenarios
[acr-task-credentials]:       container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task
[acr-tokens]:                   ./container-registry-repository-scoped-permissions.md
[aci]:                          https://aka.ms/aci
[alpine-public-image]:          https://hub.docker.com/_/alpine
[docker-hub]:                   https://hub.docker.com
[docker-hub-tokens]:            https://hub.docker.com/settings/security
[git-token]:                    https://github.com/settings/tokens
[gcr]:                          https://cloud.google.com/container-registry
[ghcr]:                         https://docs.github.com/en/free-pro-team@latest/packages/getting-started-with-github-container-registry/about-github-container-registry
[helm-charts]:                  https://helm.sh
[mcr]:                          https://aka.ms/mcr
[nginx-public-image]:           https://hub.docker.com/_/nginx
[oci-artifacts]:                ./container-registry-oci-artifacts.md
[oci-consuming-public-content]: https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/
[opa]:                          https://www.openpolicyagent.org/
[quay]:                         https://quay.io