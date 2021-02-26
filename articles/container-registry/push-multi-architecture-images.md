---
title: Images für mehrere Architekturen in einer Registrierung
description: Verwenden Sie Ihre Azure Container Registry-Instanz zum Erstellen, Importieren, Speichern und Bereitstellen von Images für mehrere Architekturen.
ms.topic: article
ms.date: 02/07/2021
ms.custom: ''
ms.openlocfilehash: f8467cd3108ae4faea9ecb4c9d9ae339f476c311
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100012280"
---
# <a name="multi-architecture-images-in-your-azure-container-registry"></a>Images für mehrere Architekturen in einer Azure Container Registry-Instanz

In diesem Artikel werden *Images für mehrere Architekturen* (auch als *Multi-Arch-Images* bezeichnet) vorgestellt, und es wird und erläutert, wie Sie diese mithilfe der Funktionen von Azure Container Registry erstellen, speichern und verwenden. 

Ein Multi-Arch-Image ist ein Typ von Containerimage, das Varianten für verschiedene Architekturen und manchmal für verschiedene Betriebssysteme kombinieren kann. Wenn Sie ein Image mit Unterstützung für mehrere Architekturen ausführen, wählen die Containerclients automatisch eine Imagevariante aus, die Ihrem Betriebssystem und Ihrer Architektur entspricht.

## <a name="manifests-and-manifest-lists"></a>Manifeste und Manifestlisten

Multi-Arch-Images basieren auf Imagemanifesten und -manifestlisten.

### <a name="manifest"></a>Manifest

Jedes Containerimage wird durch ein [Manifest](container-registry-concepts.md#manifest) repräsentiert. Ein Manifest ist eine JSON-Datei, die das Image eindeutig identifiziert und auf seine Ebenen und die zugehörigen Größen verweist. 

Ein einfaches Manifest für ein Linux-`hello-world`-Image sieht in etwa wie folgt aus:

  ```json
  {
    "schemaVersion": 2,
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "config": {
        "mediaType": "application/vnd.docker.container.image.v1+json",
        "size": 1510,
        "digest": "sha256:fbf289e99eb9bca977dae136fbe2a82b6b7d4c372474c9235adc1741675f587e"
      },
    "layers": [
        {
          "mediaType": "application/vnd.docker.image.rootfs.diff.tar.gzip",
          "size": 977,
          "digest": "sha256:2c930d010525941c1d56ec53b97bd057a67ae1865eebf042686d2a2d18271ced"
        }
      ]
  }
  ```
    
Sie können ein Manifest in Azure Container Registry über das Azure-Portal oder mithilfe von Tools anzeigen, z. B. mit dem Befehl [az acr repository show-manifests](/cli/azure/acr/repository#az_acr_repository_show_manifests) in der Azure-Befehlszeilenschnittstelle.

### <a name="manifest-list"></a>Manifestliste

Eine *Manifestliste* für ein Multi-Arch-Image (allgemein als [Imageindex](https://github.com/opencontainers/image-spec/blob/master/image-index.md) für OCI-Images bezeichnet) ist eine Sammlung (ein Index) von Images. Die Erstellung erfolgt durch die Angabe der Imagenamen. Sie enthält Details zu den einzelnen Images, z. B. das unterstützte Betriebssystem und die Architektur, die Größe und den Manifest-Digest. Die Manifestliste kann auf die gleiche Weise wie ein Imagename in den Befehlen `docker pull` und `docker run` verwendet werden. 

In der `docker`-Befehlszeilenschnittstelle verwalten Sie Manifeste und Manifestlisten mit dem Befehl [docker manifest](https://docs.docker.com/engine/reference/commandline/manifest/).

> [!NOTE]
> Derzeit sind der Befehl `docker manifest` und die zugehörigen Unterbefehle experimentell. Ausführliche Informationen zur Verwendung von experimentellen Befehlen finden Sie in der Docker-Dokumentation.

Mit dem Befehl `docker manifest inspect` können Sie eine Manifestliste anzeigen. Im Folgenden finden Sie die Ausgabe für das Multi-Arch-Image `mcr.microsoft.com/mcr/hello-world:latest`, das drei Manifeste enthält: zwei für Linux-Betriebssystemarchitekturen und eines für eine Windows-Architektur.
```json
{
  "schemaVersion": 2,
  "mediaType": "application/vnd.docker.distribution.manifest.list.v2+json",
  "manifests": [
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 524,
      "digest": "sha256:83c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a",
      "platform": {
        "architecture": "amd64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 525,
      "digest": "sha256:873612c5503f3f1674f315c67089dee577d8cc6afc18565e0b4183ae355fb343",
      "platform": {
        "architecture": "arm64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 1124,
      "digest": "sha256:b791ad98d505abb8c9618868fc43c74aa94d08f1d7afe37d19647c0030905cae",
      "platform": {
        "architecture": "amd64",
        "os": "windows",
        "os.version": "10.0.17763.1697"
      }
    }
  ]
}
```

Wenn in Azure Container Registry eine Manifestliste mit Multi-Arch-Images gespeichert wird, können Sie die Manifestliste auch im Azure-Portal oder mit Tools wie dem Befehl [az acr repository show-manifests](/cli/azure/acr/repository#az_acr_repository_how_manifests) anzeigen.

## <a name="import-a-multi-arch-image"></a>Importieren von Multi-Arch-Images 

Sie können ein vorhandenes Multi-Arch-Image mit dem Befehl [az acr import](/cli/azure/acr#az_acr_import) in eine Azure Container Registry-Instanz importieren. Die Syntax für den Imageimport ist identisch mit dem für das Importieren eines Images für eine einzelne Architektur. Wie beim Importieren eines Images für eine einzelne Architektur werden bei einem Import eines Multi-Arch-Images keine Docker-Befehle verwendet. 

Weitere Informationen finden Sie unter [Importieren von Containerimages in eine Containerregistrierung](container-registry-import-images.md).

## <a name="push-a-multi-arch-image"></a>Pushen von Multi-Arch-Images

Wenn Sie Buildworkflows für das Erstellen von Containerimages für verschiedene Architekturen anwenden, führen Sie die folgenden Schritte aus, um ein Multi-Arch-Image an Ihre Azure Container Registry-Instanz zu pushen.

1. Markieren Sie jedes architekturspezifische Image, und pushen Sie es in Ihre Containerregistrierung. Im folgenden Beispiel wird von zwei Linux-Architekturen ausgegangen: arm64 und amd64. 

   ```console
   docker tag myimage:arm64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:arm64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:arm64
 
   docker tag myimage:amd64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:amd64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ``` 

1. Führen Sie `docker manifest create` aus, um eine Manifestliste zu erstellen und die vorhergehenden Images in einem Multi-Arch-Image zusammenzufassen.

   ```console
   docker manifest create myregistry.azurecr.io/multi-arch-samples/myimage:multi \
    myregistry.azurecr.io/multi-arch-samples/myimage:arm64 \
    myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ```

1. Pushen Sie das Manifest mit `docker manifest push` in Ihre Containerregistrierung:

   ```console
   docker manifest push myregistry.azurecr.io/multi-arch-samples/myimage:multi
   ```

1. Verwenden Sie den Befehl `docker manifest inspect`, um die Manifestliste anzuzeigen. Ein Beispiel für die Befehlsausgabe finden Sie in einem der vorangegangenen Abschnitte.

Nachdem Sie das Manifest für das Multi-Arch-Image in Ihre Registrierung gepusht haben, können Sie mit ihm genauso wie mit einem Image für eine einzelne Architektur arbeiten. Sie pullen das Image beispielsweise mithilfe von `docker pull`, und mit den [az acr repository](/cli/azure/acr/repository#az_acr_repository)-Befehlen können Sie Tags, Manifeste und andere Eigenschaften des Images anzeigen.

## <a name="build-and-push-a-multi-arch-image"></a>Erstellen und Pushen von Multi-Arch-Images

Mithilfe der Features von [ACR Tasks](container-registry-tasks-overview.md) können Sie ein Multi-Arch-Image erstellen und in Ihre Azure Container Registry-Instanz pushen. Definieren Sie z. B. eine [mehrstufige Aufgabe](container-registry-tasks-multi-step.md) in einer YAML-Datei, die ein Multi-Arch-Image für Linux erstellt.

Im folgenden Beispiel wird davon ausgegangen, dass Sie über separate Dockerfiles für zwei Architekturen (arm64 und amd64) verfügen. Die architekturspezifischen Images werden erstellt und gepusht. Anschließend wird ein Manifest für ein Multi-Arch-Image mit dem Tag `latest` erstellt und gepusht:

```yml
version: v1.1.0

steps:
- build: -t {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64 -f dockerfile.arm64 . 
- build: -t {{.Run.Registry}}/multi-arch-samples/myyimage:{{.Run.ID}}-arm64 -f dockerfile.amd64 . 
- push: 
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: >
    docker manifest create
    {{.Run.Registry}}/multi-arch-samples/myimage:latest
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: docker manifest push --purge {{.Run.Registry}}/multi-arch-samples/myimage:latest
- cmd: docker manifest inspect {{.Run.Registry}}/multi-arch-samples/myimage:latest
```

## <a name="next-steps"></a>Nächste Schritte

* Verwenden Sie [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines.md) zum Erstellen von Containerimages für verschiedene Architekturen.
* Erfahren Sie mehr über das Entwickeln von Images für mehrere Plattformen mithilfe des experimentellen Docker-Plug-Ins [buildx](https://docs.docker.com/buildx/working-with-buildx/).

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
