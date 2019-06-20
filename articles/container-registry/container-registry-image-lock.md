---
title: Sperren von Images in Azure Container Registry
description: Festlegen von Attributen für ein Containerimage oder Repository, sodass es in einer Azure-Containerregistrierung nicht gelöscht oder überschrieben werden kann.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 02/19/2019
ms.author: danlep
ms.openlocfilehash: ebbfaba158e7ddb669111f097eb1adde2373aa6c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60828646"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Sperren von Containerimages in einer Azure-Containerregistrierung

Sie können eine Imageversion oder ein Repository in einer Azure-Containerregistrierung sperren, damit es nicht gelöscht oder aktualisiert werden kann. Aktualisieren Sie die Attribute mithilfe des Azure CLI-Befehls [az acr repository update][az-acr-repository-update], um ein Image oder ein Repository zu sperren. 

Für die Vorgehensweisen in diesem Artikel ist erforderlich, dass Sie die Azure CLI in Azure Cloud Shell oder lokal ausführen (Version 2.0.55 oder höher werden empfohlen). Führen Sie `az --version` aus, um die Version zu finden. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Installieren von Azure CLI 2.0][azure-cli] Informationen dazu.

## <a name="scenarios"></a>Szenarien

Markierte Images sind in Azure Container Registry standardmäßig *änderbar*. Mit den entsprechenden Berechtigungen können Sie ein Image also wiederholt mit dem gleichen Tag aktualisieren und an eine Registrierung pushen. Containerimages können bei Bedarf auf [gelöscht](container-registry-delete.md) werden. Dieses Verhalten ist nützlich, wenn Sie Images entwickeln und eine Größe für Ihre Registrierung einhalten müssen.

Wenn Sie jedoch ein Containerimage für die Produktionsumgebung bereitstellen, benötigen Sie möglicherweise ein *unveränderliches* Containerimage. Ein unveränderliches Containerimage kann nicht versehentlich gelöscht oder überschrieben werden. Verwenden Sie den Befehl [az acr repository update][az-acr-repository-update], um Repository-Attribute festzulegen, wodurch Folgendes ermöglicht wird:

* Sperren einer Imageversion oder eines gesamten Repositorys

* Schützen einer Imageversion oder eines Repositorys mit zugelassenen Updates

* Verhindern von Lesevorgängen (Pull) für eine Imageversion oder ein gesamtes Repository

Beispiele hierzu finden Sie in den nachfolgenden Abschnitten.

## <a name="lock-an-image-or-repository"></a>Sperren von Images oder Repositorys 

### <a name="show-the-current-repository-attributes"></a>Anzeigen der aktuellen Repository-Attribute
Führen Sie den folgenden Befehl [az acr repository show][az-acr-repository-show] aus, um die aktuellen Attribute eines Repositorys anzuzeigen:

```azurecli
az acr repository show \
    --name myregistry --repository myrepo
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Anzeigen der aktuellen Imageattribute
Führen Sie den folgenden Befehl [az acr repository show][az-acr-repository-show] aus, um die aktuellen Attribute eines Tags anzuzeigen:

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Sperren von Images mithilfe von Tags

Führen Sie den folgenden [az acr repository update][az-acr-repository-update]-Befehl aus, um das Image *myrepo/myimage:tag* in *myregistry* zu sperren:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Sperren von Images anhand von Manifest-Digests

Führen Sie den folgenden Befehl aus, um ein *myrepo/myimage*-Image zu sperren, das durch ein Manifest-Digest identifiziert wird (SHA-256-Hash, der als `sha256:...` dargestellt wird). (Führen Sie den Befehl [az acr repository show-manifests][az-acr-repository-show-manifests] aus, um den Manifest-Digest zu ermitteln, der mindestens einem Imagetag zugeordnet ist.)

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Sperren eines Repositorys

Führen Sie den folgenden Befehl aus, um das Repository *myrepo/myimage* und alle darin enthaltenen Images zu sperren:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Schützen von Images und Repositorys vor dem Löschen

### <a name="protect-an-image-from-deletion"></a>Schützen von Images vor dem Löschen

Führen Sie den folgenden Befehl aus, um die Aktualisierung, aber nicht das Löschen, des Images *myrepo/myimage:tag* zuzulassen:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Schützen von Repositorys vor dem Löschen

Mit dem folgenden Befehl wird das Repository *myrepo/myimage* so festgelegt, dass es nicht gelöscht werden kann. Einzelne Images können weiterhin aktualisiert oder gelöscht werden.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Verhindern von Lesevorgängen für Images oder Repositorys

Führen Sie den folgenden Befehl aus, um Lesevorgänge (Pull) für das Image *myrepo/myimage:tag* zu verhindern:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Führen Sie den folgenden Befehl aus, um Lesevorgänge für das Repository *myrepo/myimage* zu verhindern:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Entsperren von Images oder Repositorys

Führen Sie den folgenden Befehl aus, um das Standardverhalten des Images *myrepo/myimage:tag* wiederherzustellen, damit es wieder gelöscht und aktualisiert werden kann:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Führen Sie den folgenden Befehl aus, um das Standardverhalten des Repositorys *myrepo/myimage* und der enthaltenen Images wiederherzustellen, damit sie wieder gelöscht und aktualisiert werden können:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie den Befehl [az acr repository update][az-acr-repository-update] verwenden, um das Löschen und Aktualisieren von Imageversionen in einem Repository zu verhindern. Informationen zum Festlegen zusätzlicher Attribute finden Sie in der Referenz zum Befehl [az acr repository update][az-acr-repository-update].

Mit dem Befehl [az acr repository show][az-acr-repository-show] können Sie die für eine Imageversion oder ein Repository festgelegten Attribute anzeigen.

Informationen zu Löschvorgängen finden Sie unter [Löschen von Containerimages in Azure Container Registry][container-registry-delete].

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

