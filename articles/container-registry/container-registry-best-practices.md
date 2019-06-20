---
title: Bewährte Methoden in Azure Container Registry
description: Erfahren Sie, wie Sie Azure Container Registry anhand dieser bewährten Methoden effektiv verwenden.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 09/27/2018
ms.author: danlep
ms.openlocfilehash: 2cf64c7c4f99a57c4a4a6cf03e68e8af803ceca9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60787349"
---
# <a name="best-practices-for-azure-container-registry"></a>Bewährte Methoden für Azure Container Registry

Durch Befolgen dieser bewährten Methoden sorgen Sie für eine optimale Leistung und kostengünstige Verwendung Ihrer privaten Docker-Registrierung in Azure.

## <a name="network-close-deployment"></a>Netzwerknahe Bereitstellung

Erstellen Sie Ihre Containerregistrierung in derselben Azure-Region, in der Sie Container bereitstellen. Indem Ihre Registrierung in eine Region platziert wird, die sich in Netzwerknähe Ihrer Containerhosts befindet, können Sie sowohl Wartezeiten als auch Kosten senken.

Die netzwerknahe Bereitstellung ist einer der Hauptgründe für die Verwendung einer privaten Containerregistrierung. Docker-Images verfügen über ein effizientes [Ebenenkonstrukt](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/), das inkrementelle Bereitstellungen ermöglicht. Allerdings müssen neue Knoten alle Ebenen mithilfe von Pull übertragen, die für ein bestimmtes Image erforderlich sind. Bei diesem anfänglichen `docker pull` können schnell mehrere Gigabyte zusammenkommen. Durch eine private Registrierung in der Nähe Ihrer Bereitstellung wird die Netzwerklatenz auf ein Mindestmaß verringert.
Darüber hinaus werden für alle öffentlichen Clouds, einschließlich Azure, Gebühren für ausgehenden Netzwerkdatenverkehr erhoben. Wenn Images aus einem Rechenzentrum in ein anderes übertragen werden, entstehen neben Wartezeiten auch Kosten für ausgehenden Netzwerkdatenverkehr.

## <a name="geo-replicate-multi-region-deployments"></a>Georeplikation von Bereitstellungen in mehreren Regionen

Verwenden Sie das Feature [Georeplikation](container-registry-geo-replication.md) von Azure Container Registry, wenn Sie Container in mehreren Regionen bereitstellen. Ganz gleich, ob Sie globale Kunden von lokalen Rechenzentren aus bedienen oder Ihr Entwicklungsteam sich an unterschiedlichen Standorten befindet – durch die Georeplikation Ihrer Registrierung können Sie die Registrierungsverwaltung vereinfachen und Wartezeiten auf ein Minimum reduzieren. Georeplikation ist nur mit [Premium](container-registry-skus.md)-Registrierungen verfügbar.

Sehen Sie sich die dreiteilige Tutorialreihe [Georeplikation in Azure Container Registry](container-registry-tutorial-prepare-registry.md) an, um sich über die Verwendung der Georeplikation zu informieren.

## <a name="repository-namespaces"></a>Repositorynamespaces

Mithilfe von Repositorynamespaces können Sie die gemeinsame Nutzung einer einzigen Registrierung in mehreren Gruppen innerhalb Ihrer Organisation ermöglichen. Registrierungen können in mehreren Bereitstellungen und Teams gemeinsam verwendet werden. Azure Container Registry unterstützt geschachtelte Namespaces und ermöglicht damit die Isolation von Gruppen.

Sehen Sie sich beispielsweise die folgenden Containerimagetags an. Images, die unternehmensweit verwendet werden (z.B. `aspnetcore`), werden im Stammnamespace abgelegt, während Containerimages, die zu den Produktions- und Marketinggruppen gehören, jeweils eigene Namespaces verwenden.

```
contoso.azurecr.io/aspnetcore:2.0
contoso.azurecr.io/products/widget/web:1
contoso.azurecr.io/products/bettermousetrap/refundapi:12.3
contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42
```

## <a name="dedicated-resource-group"></a>Dedizierte Ressourcengruppe

Da Containerregistrierungen Ressourcen sind, die über mehrere Containerhosts verwendet werden, sollte sich eine Registrierung in einer eigenen Ressourcengruppe befinden.

Auch wenn Sie mit einem bestimmten Hosttyp (z.B. Azure Container Instances) experimentieren, möchten Sie die Containerinstanz wahrscheinlich am Ende löschen. Möglicherweise empfiehlt es sich jedoch, die Sammlung von Images beizubehalten, die Sie mithilfe von Push in Azure Container Registry übertragen haben. Indem Sie Ihre Registrierung in einer eigenen Ressourcengruppe ablegen, minimieren Sie das Risiko, dass die Sammlung von Images in der Registrierung versehentlich gelöscht wird, wenn Sie die Ressourcengruppe der Containerinstanz löschen.

## <a name="authentication"></a>Authentication

Bei der Authentifizierung bei einer Azure-Containerregistrierung gibt es im Wesentlichen zwei Szenarien: die individuelle Authentifizierung und die Dienstauthentifizierung (oder „monitorlose Authentifizierung“). Die folgende Tabelle enthält eine kurze Übersicht über diese Szenarien sowie die jeweils empfohlene Authentifizierungsmethode.

| Type | Beispielszenario | Empfohlene Methode |
|---|---|---|
| Einzelne Identität | Ein Entwickler, der Images mittels Pull auf seinen bzw. mittels Push von seinem Entwicklungscomputer überträgt. | [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) |
| Monitorlose/Dienstidentität | Build- und Bereitstellungspipelines, an denen der Benutzer nicht direkt beteiligt ist. | [Dienstprinzipal](container-registry-authentication.md#service-principal) |

Ausführliche Informationen zur Azure Container Registry-Authentifizierung finden Sie unter [Authentifizieren bei einer Azure-Containerregistrierung](container-registry-authentication.md).

## <a name="manage-registry-size"></a>Verwalten von Registrierungsgrößen

Die Speichereinschränkungen jeder [SKU der Containerregistrierung][container-registry-skus] sollten auf ein typisches Szenario ausgerichtet werden: **Basic** für die ersten Schritte, **Standard** für die Mehrzahl der Produktionsanwendungen und **Premium** für Leistung mit Hyperskalierung und [Georeplikation][container-registry-geo-replication]. Während der gesamten Dauer Ihrer Registrierung sollten Sie die Größe durch regelmäßiges Löschen von ungenutztem Inhalt verwalten.

Verwenden Sie den Azure CLI-Befehl [az acr show-usage][az-acr-show-usage], um die aktuelle Größe Ihrer Registrierung anzuzeigen:

```console
$ az acr show-usage --resource-group myResourceGroup --name myregistry --output table
NAME      LIMIT         CURRENT VALUE    UNIT
--------  ------------  ---------------  ------
Size      536870912000  185444288        Bytes
Webhooks  100                            Count
```

Sie finden die aktuelle Speichernutzung auch in der **Übersicht** Ihrer Registrierung im Azure-Portal:

![Informationen zur Registrierungsnutzung im Azure-Portal][registry-overview-quotas]

### <a name="delete-image-data"></a>Löschen von Imagedaten

Azure Container Registry unterstützt mehrere Methoden zum Löschen von Imagedaten aus Ihrer Containerregistrierung. Sie können Images anhand von Tags oder Manifesthashes löschen oder ein gesamtes Repository löschen.

Weitere Informationen zum Löschen von Imagedaten aus Ihrer Registrierung, einschließlich nicht mit Tags gekennzeichneter Images (manchmal als „verbleibend“ oder „verwaist“ bezeichnet) finden Sie unter [Löschen von Containerimages in Azure Container Registry](container-registry-delete.md).

## <a name="next-steps"></a>Nächste Schritte

Azure Container Registry ist in mehrere Tarifen (SKUs) verfügbar, die jeweils verschiedene Funktionen bereitstellen. Ausführliche Informationen zu verfügbaren SKUs finden Sie unter [Azure Container Registry-SKUs](container-registry-skus.md).

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-show-usage]: /cli/azure/acr#az-acr-show-usage
[azure-cli]: /cli/azure
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md
