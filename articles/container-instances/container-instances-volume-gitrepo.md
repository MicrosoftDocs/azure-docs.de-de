---
title: Einbinden eines gitRepo-Volumes in Azure Container Instances
description: Hier erfahren Sie, wie Sie ein gitRepo-Volume einbinden, um ein Git-Repository in Ihren Containerinstanzen zu klonen.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 06/15/2018
ms.author: danlep
ms.openlocfilehash: 70593bffbf30b3a0c0978e56c2af1a856a22f2ec
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60563018"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Einbinden eines gitRepo-Volumes in Azure Container Instances

Hier erfahren Sie, wie Sie ein *gitRepo*-Volume einbinden, um ein Git-Repository in Ihren Containerinstanzen zu klonen.

> [!NOTE]
> Zurzeit ist das Einbinden eines *gitRepo*-Volumes auf Linux-Container beschränkt. Bis alle Features auch für Windows-Container verfügbar sind, finden Sie die aktuellen Plattformunterschiede unter [Kontingente und Regionsverfügbarkeit für Azure Container Instances](container-instances-quotas.md).

## <a name="gitrepo-volume"></a>GitRepo-Volume

Das *gitRepo*-Volume bindet ein Verzeichnis ein und klont darin beim Containerstart das angegebene Git-Repository. Bei Verwendung eines *gitRepo*-Volumes in Ihren Containerinstanzen müssen Sie dazu keinen Code in Ihren Anwendungen hinzufügen.

Wenn Sie ein *gitRepo*-Volume einbinden, können Sie zum Konfigurieren des Volumes drei Eigenschaften festlegen:

| Eigenschaft | Erforderlich | BESCHREIBUNG |
| -------- | -------- | ----------- |
| `repository` | Ja | Die vollständige URL (einschließlich `http://` bzw. `https://`) des zu klonenden Git-Repositorys|
| `directory` | Nein | Das Verzeichnis, in dem das Repository geklont werden soll. Der Pfad darf `..` weder enthalten noch darf er damit beginnen.  Bei Angabe von `.` wird das Repository im Verzeichnis des Volumes geklont. Andernfalls wird das Git-Repository in einem Unterverzeichnis mit dem angegebenen Namen im Volumeverzeichnis geklont. |
| `revision` | Nein | Der Commithash der zu klonenden Version. Wird er nicht angegeben, wird die `HEAD`-Revision geklont. |

## <a name="mount-gitrepo-volume-azure-cli"></a>Einbinden eines gitRepo-Volumes: Azure-Befehlszeilenschnittstelle

Um ein gitRepo-Volume einzubinden, wenn Sie Containerinstanzen mit der [Azure CLI](/cli/azure) bereitstellen, geben Sie für den Befehl [az container create][az-container-create] die Parameter `--gitrepo-url` und `--gitrepo-mount-path` an. Sie können optional das Verzeichnis innerhalb des zu klonenden Volumes (`--gitrepo-dir`) und den Commithash der zu klonenden Revision (`--gitrepo-revision`) angeben.

Dieser Beispielbefehl klont die Microsoft-Beispielanwendung [aci-helloworld][aci-helloworld] in `/mnt/aci-helloworld` in der Containerinstanz:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name hellogitrepo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --dns-name-label aci-demo \
    --ports 80 \
    --gitrepo-url https://github.com/Azure-Samples/aci-helloworld \
    --gitrepo-mount-path /mnt/aci-helloworld
```

Um zu überprüfen, ob das gitRepo-Volume eingebunden wurde, starten Sie eine Shell im Container mit [az container exec][az-container-exec], und listen Sie das Verzeichnis auf:

```console
$ az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>Einbinden eines gitRepo-Volumes: Ressourcen-Manager

Um ein gitRepo-Volume einzubinden, wenn Sie Containerinstanzen mit einer [Azure Resource Manager-Vorlage](/azure/templates/microsoft.containerinstance/containergroups) bereitstellen, füllen Sie zuerst das Array `volumes` im Containergruppenabschnitt `properties` der Vorlage aus. Füllen Sie dann für jeden Container in der Containergruppe, in der Sie das *gitRepo*-Volume einbinden möchten, das Array `volumeMounts` im Abschnitt `properties` der Containerdefinition auf.

Mit der folgenden Resource Manager-Vorlage wird beispielsweise eine Containergruppe mit einem einzelnen Container erstellt. Der Container klont zwei GitHub-Repositorys, die durch die *gitRepo*-Volumeblöcke angegeben werden. Das zweite Volume enthält zusätzliche Eigenschaften, die ein Verzeichnis zum Klonen angeben, sowie den Commithash einer spezifischen Revision, die geklont werden soll.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

Die Verzeichnisstruktur der zwei geklonten Repositorys, die in der vorhergehenden Vorlage definiert werden, sieht wie folgt aus:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Ein Beispiel für die Bereitstellung der Containerinstanzen mit einer Azure Resource Manager-Vorlage finden Sie unter [Bereitstellen von Gruppen mit mehreren Containern in Azure Container Instances](container-instances-multi-container-group.md).

## <a name="private-git-repo-authentication"></a>Authentifizierung eines privaten Git-Repositorys

Zum Einbinden eines gitRepo-Volumes für ein privates Git-Repository geben Sie Anmeldeinformationen in der Repository-URL an. Anmeldeinformationen bestehen in der Regel aus einem Benutzernamen und einem persönlichen Zugriffstoken (PAT), das bereichsbezogenen Zugriff auf das Repository gewährt.

Beispiel: Der `--gitrepo-url`-Parameter der Azure CLI für ein privates GitHub-Repository würde etwa wie folgt aussehen (dabei steht „gituser“ für den GitHub-Benutzernamen, und „abcdef1234fdsa4321abcdef“ ist das persönliche Zugriffstoken des Benutzers):

```azurecli
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

Für ein Azure Repos Git-Repository geben Sie einen Benutzernamen (Sie können wie im folgenden Beispiel „azurereposuser“ verwenden) in Kombination mit einem gültigen PAT an:

```azurecli
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

Weitere Informationen über persönliche Zugriffstoken für GitHub und Azure Repos finden Sie unter:

GitHub: [Creating a personal access token for the command line][pat-github] (Erstellen eines persönlichen Zugriffstokens für die Befehlszeile)

Azure Repos: [Create personal access tokens to authenticate access][pat-repos] (Erstellen von persönlichen Zugriffstoken zur Authentifizierung des Zugriffs)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie andere Volumetypen in Azure Container Instances bereitgestellt werden:

* [Einbinden einer Azure-Dateifreigabe in Azure Container Instances](container-instances-volume-azure-files.md)
* [Einbinden eines emptyDir-Volumes in Azure Container Instances](container-instances-volume-emptydir.md)
* [Einbinden eines geheimen Volumes in Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-repos]: https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
