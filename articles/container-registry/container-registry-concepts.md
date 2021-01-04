---
title: Informationen zu Repositorys und Images
description: Einführung in die grundlegenden Konzepte von Azure-Containerregistrierungen, Repositorys und Containerimages.
ms.topic: article
ms.date: 06/16/2020
ms.openlocfilehash: cd2f93c119817c722401f7290064894f3d39dac9
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335893"
---
# <a name="about-registries-repositories-and-images"></a>Informationen zu Registrierungen, Repositorys und Images

Dieser Artikel stellt die grundlegenden Konzepte von Containerregistern, Repositorys, Containerimages und verwandten Artefakten vor. 

## <a name="registry"></a>Registrierung

Eine Container- *Registrierung* ist ein Dienst, der Containerimages verteilt und speichert. Docker Hub ist eine öffentliche Containerregistrierung, die die Open-Source-Community unterstützt und als allgemeiner Katalog von Images dient. Azure Container Registry bietet Benutzern die direkte Steuerung ihrer Images mit integrierter Authentifizierung, [Georeplikation](container-registry-geo-replication.md), die die globale Verteilung und Zuverlässigkeit netzwerknaher Bereitstellungen unterstützt, [Konfiguration von virtuellen Netzwerken und Firewalls](container-registry-vnet.md), [Tagsperren](container-registry-image-lock.md) und vielen anderen erweiterten Funktionen. 

Zusätzlich zu den Docker-Containerimages unterstützt Azure Container Registry verwandte [Inhaltsartefakte](container-registry-image-formats.md) einschließlich Open Container Initiative-Imageformaten (OCI).

## <a name="content-addressable-elements-of-an-artifact"></a>Inhaltsadressierbare Elemente eines Artefakts

Die Adresse eines Elements in einer Azure-Containerregistrierung enthält die folgenden Elemente. 

`[loginUrl]/[repository:][tag]`

* **loginUrl** : Der vollqualifizierte Name des Registrierungshosts. Der Registrierungshost in einer Azure-Containerregistrierung hat das Format *meineregistrierung*.azurecr.io (nur Kleinbuchstaben). Sie müssen die loginUrl angeben, wenn Sie Docker oder andere Clienttools verwenden, um Artefakte in eine Azure-Containerregistrierung zu pullen oder pushen. 
* **Repository** : Name einer logischen Gruppierung mit mindestens einem verknüpften Image oder Artefakt, z. B. die Images für eine Anwendung oder ein Basisbetriebssystem. Kann einen *Namespacepfad* enthalten. 
* **Tag** : Bezeichner für eine bestimmte Version eines in einem Repository gespeicherten Images oder Artefakts.

Beispielsweise kann der vollständige Name eines Images in einer Azure-Containerregistrierung so aussehen:

*myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2*

Weitere Informationen zu diesen Elementen finden Sie in den folgenden Abschnitten.

## <a name="repository-name"></a>Name des Repositorys

Ein *Repository* ist eine Sammlung von Containerimages oder anderen Artefakten mit dem gleichen Namen, aber anderen Tags. Die folgenden drei Images befinden sich beispielsweise im Repository „acr-helloworld“:


- *acr-helloworld:latest*
- *acr-helloworld:v1*
- *acr-helloworld:v2*

Repository-Namen können auch [Namespaces](container-registry-best-practices.md#repository-namespaces) enthalten. Mit Namespaces können Sie verwandte Repositorys und den Artefaktbesitz in Ihrer Organisation identifizieren, indem Sie durch Schrägstriche getrennte Namen verwenden. Allerdings verwaltet die Registrierung alle Repositorys unabhängig voneinander und nicht als Hierarchie. Zum Beispiel:

- *marketing/campaign10-18/web:v2*
- *marketing/campaign10-18/api:v3*
- *marketing/campaign10-18/email-sender:v2*
- *product-returns/web-submission:20180604*
- *product-returns/legacy-integrator:20180715*

Repositorynamen dürfen nur alphanumerische Kleinbuchstaben, Punkte, Bindestriche, Unterstriche und Schrägstriche enthalten. 

Vollständige Repositorybenennungsregeln finden Sie in der Spezifikation der [Open Container Initiative-Distribution](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview).

## <a name="image"></a>Image

Ein Containerimage oder sonstiges Artefakt in einer Registrierung wird einem oder mehreren Tags zugeordnet, verfügt über eine oder mehrere Ebenen und wird durch ein Manifest identifiziert. Wenn Sie wissen, wie diese Komponenten miteinander in Beziehung stehen, können Sie Ihre Registrierung effektiv verwalten.

### <a name="tag"></a>Tag

Das *Tag* für ein Image oder anderes Artefakt gibt die Version an. Einem einzelnen Artefakt in einem Repository kann eines oder mehrere Tags zugewiesen werden, und Tags können auch entfernt werden. Sie können also alle Tags aus einem Image löschen, während die Daten des Images (die Ebenen) in der Registrierung verbleiben.

Das Repository (oder Repository und Namespace) plus ein Tag definieren den Namen eines Images. Sie können Push- und Pull-Vorgänge für Images ausführen, indem Sie deren Namen im Push- oder Pull-Vorgang festlegen. Das Tag `latest` wird standardmäßig verwendet, wenn Sie keine Angabe in Ihren Docker-Befehlen bereitstellen.

Wie Sie Containerimages mit Tags versehen, hängt von den Szenarien ihrer Entwicklung oder Bereitstellung ab. So werden beispielsweise dauerhafte Tags für die Pflege Ihrer Basisimages und eindeutige Tags für die Bereitstellung von Images empfohlen. Weitere Informationen finden Sie unter [Empfehlungen für das Taggen und die Versionsverwaltung von Containerimages](container-registry-image-tag-version.md).

Informationen zu Benennungsregeln für Tags finden Sie in der [Docker-Dokumentation](https://docs.docker.com/engine/reference/commandline/tag/).

### <a name="layer"></a>Ebene

Containerimages bestehen aus einer oder mehreren *Ebenen* , die jeweils einer Zeile in der Dockerfile-Datei entsprechen, die das Image definiert. Images in einer Registrierung teilen allgemeine Ebenen, die die Speichereffizienz verbessern. Z.B. können mehrere Bilder in verschiedenen Repositorys dieselbe Alpine Linux-Basisebene teilen, aber nur eine Kopie dieser Ebene wird in der Registrierung gespeichert.

Die Ebenenfreigabe optimiert außerdem die Ebenenverteilung auf Knoten mit mehreren Images, die gemeinsame Ebenen verwenden. Wenn ein Image auf einem Knoten beispielsweise die Alpine Linux-Ebene als Basis enthält, wird beim nachfolgenden Pull-Vorgang eines anderen Images, das auf dieselbe Ebene des Knotens verweist, die Ebene nicht auf den Knoten übertragen. Stattdessen verweist es auf die Ebene, die bereits auf dem Knoten vorhanden ist.

Um sichere Isolation und Schutz vor potenzieller Ebenenmanipulation zu ermöglichen, werden die Ebenen nicht Registrierungen übergreifend freigegeben.

### <a name="manifest"></a>Manifest

Jedes Containerimage oder Artefakt, das per Push an eine Containerregistrierung übertragen wird, ist einem *Manifest* zugeordnet. Das Manifest wird von der Registrierung generiert, wenn das Image per Push übertragen wird. Es identifiziert das Image und gibt die Ebenen an. Sie können die Manifeste für ein Repository mit dem Azure CLI-Befehl [az acr repository show-manifests][az-acr-repository-show-manifests] auflisten:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Listen Sie beispielsweise die Manifeste für das Repository "acr-helloworld" auf:

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

### <a name="manifest-digest"></a>Manifest-Digest

Manifeste werden durch einen eindeutigen SHA-256-Hash oder *manifest digest* identifiziert. Jedes Image oder Artefakt – ob mit oder ohne Tag  – wird durch seinen Digest identifiziert. Der Digest-Wert ist eindeutig, auch wenn die Ebenendaten des Images mit einem anderen Image übereinstimmen. Dieser Mechanismus ermöglicht das wiederholte Pushen von Images mit identischen Tags an eine Registrierung. Beispielsweise können Sie wiederholt `myimage:latest` in Ihre Registrierung pushen, ohne einen Fehler zu erhalten, da jedes Image durch seinen eindeutigen Digest identifiziert wird.

Sie können ein Image aus einer Registrierung pullen, indem Sie den Digest im Pull-Vorgang angeben. Einige Systeme können so konfiguriert werden, dass sie Push-Vorgänge per Digest ausführen, da so sichergestellt ist, dass die Version des Images per Pull abgerufen wird, selbst wenn ein Image mit identischen Tags anschließend per Push in die Registrierung übertragen wird.

Hier ein Beispiel für das Pullen eines Images aus dem Repository „acr-helloworld“ per Manifest-Digest:

`docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`

> [!IMPORTANT]
> Wenn Sie wiederholt geänderte Bilder mit identischen Tags per Push übertragen, kann das zu verwaisten Images führen – Images ohne Tags, die Speicherplatz in der Registrierung belegen. Images ohne Tags werden beim Auflisten oder Anzeigen von Images nach Tag nicht in der Azure-Befehlszeilenschnittstelle oder im Azure-Portal angezeigt. Die Ebenen sind jedoch noch vorhanden und verbrauchen Speicherplatz in der Registrierung. Das Löschen eines nicht markierten Image gibt Speicherplatz in der Registrierung frei, wenn das Manifest das einzige oder letzte ist, das auf eine bestimmte Ebene zeigt. Informationen zum Freigeben von Speicherplatz, der von Images ohne Tags verwendet wird, finden Sie unter [Löschen von Containerimages in Azure Container Registry](container-registry-delete.md).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über [Imagespeicher](container-registry-storage.md) und die [unterstützten Inhaltsformate](container-registry-image-formats.md) in Azure Container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


