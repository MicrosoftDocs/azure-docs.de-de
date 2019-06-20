---
title: Verwenden von Helm-Repositorys in Azure Container Registry
description: Informationen zum Verwenden eines Helm-Repositorys mit Azure Container Registry zum Speichern von Diagrammen für Ihre Anwendungen
services: container-registry
author: iainfoulds
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: iainfou
ms.openlocfilehash: ba0e1386d67e920f1805d244f9042044bb462ec9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62109850"
---
# <a name="use-azure-container-registry-as-a-helm-repository-for-your-application-charts"></a>Verwenden Sie Azure Container Registry als Helm-Repository für Ihre Anwendungsdiagramme

Sie können den [Open Source-Paket-Manager von Helm][helm] verwenden, um Anwendungen für Kubernetes ohne großen Aufwand zu verwalten und bereitzustellen. Bei Helm werden Anwendungen als *Diagramme* definiert, die in einem Helm-Repository für Diagramme gespeichert werden. In diesen Diagrammen werden Konfigurationen und Abhängigkeiten definiert. Darüber hinaus können sie im gesamten Anwendungslebenszyklus mit einer Versionsangabe versehen werden. Azure Container Registry kann als Host für Helm-Repositorys für Diagramme verwendet werden.

Mit Azure Container Registry verfügen Sie über ein privates, sicheres Helm-Repository für Diagramme, das in Buildpipelines oder andere Azure-Dienste integriert werden kann. Helm-Repositorys für Diagramme in Azure Container Registry umfassen Georeplikationsfunktionen, damit Ihre Diagramme eng mit Bereitstellungen verbunden sind sowie zu Redundanzzwecken. Sie zahlen nur für den Speicher, der von den Diagrammen genutzt wird, und Ihnen stehen sämtliche Azure Container Registry-Tarife zur Verfügung.

In diesem Artikel wird die Verwendung eines Helm-Repositorys für Diagramme erläutert, das in Azure Container Registry gespeichert ist.

> [!IMPORTANT]
> Diese Funktion steht derzeit als Vorschau zur Verfügung. Wenn Sie Vorschauversionen nutzen möchten, müssen Sie die [zusätzlichen Nutzungsbedingungen][terms-of-use] akzeptieren. Einige Aspekte dieses Features werden bis zur allgemeinen Verfügbarkeit unter Umständen noch geändert.

## <a name="before-you-begin"></a>Voraussetzungen

Für die Schritte in diesem Artikel müssen folgende Voraussetzungen erfüllt sein:

- **Azure Container Registry**: Erstellen Sie in Ihrem Azure-Abonnement eine Containerregistrierung. Verwenden Sie beispielsweise das [Azure-Portal](container-registry-get-started-portal.md) oder die [Azure CLI](container-registry-get-started-azure-cli.md).
- **Version 2.11.0 oder höher des Helm-Clients (keine RC-Version):** Führen Sie `helm version` aus, um Ihre aktuelle Version zu ermitteln. Darüber hinaus benötigen Sie einen Helm-Server (Tiller), der in einem Kubernetes-Cluster initialisiert wird. Bei Bedarf können Sie [einen Azure Kubernetes Service-Cluster erstellen][aks-quickstart]. Weitere Informationen zum Installieren und Aktualisieren von Helm finden Sie unter [Installieren von Helm][helm-install].
- **Azure CLI-Version 2.0.46 oder höher** : Führen Sie `az --version` aus, um die Version zu ermitteln. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Installieren von Azure CLI 2.0][azure-cli-install] Informationen dazu.

## <a name="add-a-repository-to-helm-client"></a>Hinzufügen eines Repositorys zum Helm-Client

Ein Helm-Repository ist ein HTTP-Server, auf dem Helm-Diagramme gespeichert werden können. Azure Container Registry kann diesen Speicher für Helm-Diagramme bereitstellen und die Indexdefinition verwalten, während Sie Diagramme zum Repository hinzufügen und daraus entfernen.

Sie können Ihre Azure Container Registry als Repository für Helm-Diagramme über die Azure CLI hinzufügen. Bei diesem Ansatz wird Ihr Helm-Client mit dem URI und den Anmeldeinformationen für das von Azure Container Registry unterstützte Repository aktualisiert. Sie müssen diese Repositoryinformationen nicht manuell eingeben. Die Anmeldeinformationen werden folglich beispielsweise nicht im Befehlsverlauf verfügbar gemacht.

Melden Sie sich bei Bedarf bei der Azure CLI an, und befolgen Sie die Eingabeaufforderungen:

```azurecli
az login
```

Konfigurieren Sie mithilfe des Befehls [az configure][az-configure] die Standardwerte für die Azure CLI mit dem Namen Ihrer Azure Container Registry-Instanz. Ersetzen Sie im folgenden Beispiel `<acrName>` durch den Namen Ihrer Registrierung:

```azurecli
az configure --defaults acr=<acrName>
```

Fügen Sie nun mithilfe des Befehls [az acr helm repo add][az-acr-helm-repo-add] Ihr Repository für Helm-Diagramme der Azure Container Registry-Instanz zu Ihrem Helm-Client hinzu. Mit diesem Befehl wird ein Authentifizierungstoken für Ihre Azure Container Registry abgerufen, das vom Helm-Client verwendet wird. Das Authentifizierungstoken ist eine Stunde lang gültig. Ähnlich wie `docker login` können Sie diesen Befehl in zukünftigen CLI-Sitzungen ausführen, um Ihren Helm-Client bei Ihrem Helm-Repository für Diagramme in Azure Container Registry zu authentifizieren:

```azurecli
az acr helm repo add
```

## <a name="add-a-chart-to-the-repository"></a>Hinzufügen eines Diagramms zum Repository

Für diesen Artikel rufen wir aus dem öffentlichen Helm-Repository *stable* ein vorhandenes Helm-Diagramm ab. Bei dem Repository *stable* handelt es sich um ein zusammengestelltes, öffentliches Repository, das allgemeine Anwendungsdiagramme enthält. Paketverwalter können ihre Diagramme an das Repository *stable* übermitteln. Auf gleiche Weise stellt der Docker-Hub eine öffentliche Registrierung für allgemeine Containerimages bereit. Anschließend kann das über das Repository *stable* heruntergeladene Diagramm mithilfe von Push in Ihr privates Azure Container Registry-Repository übertragen werden. In den meisten Szenarios würden Sie für die von Ihnen entwickelten Anwendungen Ihre eigenen Diagramme erstellen und hochladen. Weitere Informationen zum Erstellen Ihrer eigenen Helm-Diagramme finden Sie unter [Entwickeln von Helm-Diagrammen][develop-helm-charts].

Erstellen Sie zunächst unter *~/acr-helm* ein Verzeichnis, und laden Sie anschließend das vorhandene Diagramm *stable/wordpress* herunter:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm fetch stable/wordpress
```

Listen Sie das heruntergeladene Diagramm auf, und notieren Sie sich die im Dateinamen enthaltene Wordpress-Version. Durch den Befehl `helm fetch stable/wordpress` wird keine bestimmte Version angegeben. Daher wurde die *aktuelle* Version abgerufen. Alle Helm-Diagramme enthalten gemäß dem Standard [SemVer 2][semver2] eine Versionsnummer im Dateinamen. In der folgenden Beispielausgabe weist das Wordpress-Diagramm die Version *2.1.10* auf:

```
$ ls

wordpress-2.1.10.tgz
```

Übertragen Sie das Diagramm nun mithilfe von Push in Azure Container Registry in Ihr Helm-Repository für Diagramme. Verwenden Sie hierzu in der Azure CLI den Befehl [az acr helm push][az-acr-helm-push]. Geben Sie den Namen des Helm-Diagramms an, das Sie im vorherigen Schritt heruntergeladen haben, wie z.B. *Wordpress-2.1.10.tgz*:

```azurecli
az acr helm push wordpress-2.1.10.tgz
```

Nach kurzer Zeit meldet die Azure CLI, dass Ihr Diagramm gespeichert wurde, wie in der folgenden Beispielausgabe gezeigt wird:

```
$ az acr helm push wordpress-2.1.10.tgz

{
  "saved": true
}
```

## <a name="list-charts-in-the-repository"></a>Auflisten von Diagrammen im Repository

Der Helm-Client verwaltet eine lokale, zwischengespeicherte Kopie der Inhalte des Remoterepositorys. Durch Änderungen an einem Remoterepository aktualisiert der Helm-Client die Liste der verfügbaren Diagramme, die ihm lokal bekannt sind, nicht automatisch. Wenn Sie über Repositorys hinweg nach Diagrammen suchen, verwendet Helm den lokal zwischengespeicherten Index. Damit das im vorherigen Schritt hochgeladene Diagramm verwendet werden kann, muss der lokale Index des Helm-Repositorys aktualisiert werden. Sie können die Repositorys im Helm-Client neu indizieren oder den Repositoryindex über die Azure CLI aktualisieren. Jedes Mal, wenn Sie ein Diagramm zu Ihrem Repository hinzufügen, müssen Sie den folgenden Schritt ausführen:

```azurecli
az acr helm repo add
```

Wenn in Ihrem Repository ein Diagramm gespeichert und der aktualisierte Index lokal verfügbar ist, können Sie einen Suchvorgang oder eine Installation mithilfe der regulären Befehle des Helm-Clients durchführen. Mit `helm search <acrName>` können Sie alle Diagramme in Ihrem Repository anzeigen. Geben Sie einen eigenen Azure Container Registry-Namen an:

```console
helm search <acrName>
```

Das Wordpress-Diagramm, das im vorherigen Schritt mithilfe von Push übertragen wurde, wird aufgelistet, wie in der folgenden Beispielausgabe gezeigt wird:

```
$ helm search myacrhelm

NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  2.1.10          4.9.8       Web publishing platform for building blogs and websites.
```

Sie können die Diagramme auch über die Azure CLI auflisten, mithilfe des Befehls [az acr helm list][az-acr-helm-list]:

```azurecli
az acr helm list
```

## <a name="show-information-for-a-helm-chart"></a>Anzeigen von Informationen zu einem Helm-Diagramm

Wenn Sie im Repository Informationen zu einem bestimmten Diagramm anzeigen möchten, können Sie wieder den regulären Helm-Client verwenden. Verwenden Sie `helm inspect`, um Informationen zu dem Diagramm mit dem Namen *Wordpress* anzuzeigen.

```console
helm inspect <acrName>/wordpress
```

Wenn keine Versionsnummer angegeben ist, wird die *aktuelle* Version verwendet. Helm Gibt ausführliche Informationen zu Ihrem Diagramm zurück, wie in der folgenden verkürzten Beispielausgabe gezeigt wird:

```
$ helm inspect myacrhelm/wordpress

appVersion: 4.9.8
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: https://www.wordpress.com/
icon: https://bitnami.com/assets/stacks/wordpress/img/wordpress-stack-220x234.png
keywords:
- wordpress
- cms
- blog
- http
- web
- application
- php
maintainers:
- email: containers@bitnami.com
  name: bitnami-bot
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 2.1.10
[...]
```

Sie können die Informationen zu einem Diagramm auch über die Azure CLI mithilfe des Befehls [az acr helm show][az-acr-helm-show] anzeigen. Auch hier wird standardmäßig die *aktuelle* Version eines Diagramms zurückgegeben. Sie können `--version` anfügen, um eine bestimmte Version eines Diagramms aufzulisten, z.B. *2.1.10*:

```azurecli
az acr helm show wordpress
```

## <a name="install-a-helm-chart-from-the-repository"></a>Installieren eines Helm-Diagramms aus dem Repository

Das Helm-Diagramm in Ihrem Repository wird installiert, indem Sie den Namen des Repositorys angeben und anschließend auf den Namen des Diagramms klicken. Sie können das Wordpress-Diagramm mithilfe des Helm-Clients installieren:

```console
helm install <acrName>/wordpress
```

> [!TIP]
> Wenn Sie das Diagramm mithilfe von Push in Ihr Helm-Repository für Diagramme in Azure Container Registry übertragen und später in einer neuen CLI-Sitzung zurückkehren, benötigt Ihr lokaler Helm-Client ein aktualisiertes Authentifizierungstoken. Mithilfe des Befehls [az acr helm repo add][az-acr-helm-repo-add] können Sie ein neues Authentifizierungstoken abrufen.

Während des Installationsprozesses werden die folgenden Schritte ausgeführt:

- Der Helm-Client durchsucht den lokalen Repositoryindex.
- Das entsprechende Diagramm wird aus dem Azure Container Registry-Repository heruntergeladen.
- Das Diagramm wird mit Tiller in Ihrem Kubernetes-Cluster bereitgestellt.

Die folgende verkürzte Beispielausgabe zeigt die Kubernetes-Ressource, die über das Helm-Diagramm bereitgestellt wurden:

```
$ helm install myacrhelm/wordpress

NAME:   irreverent-jaguar
LAST DEPLOYED: Thu Sep 13 21:44:20 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                                          READY  STATUS   RESTARTS  AGE
irreverent-jaguar-wordpress-7ff46d9b8c-b7v6m  0/1    Pending  0         1s
irreverent-jaguar-mariadb-0                   0/1    Pending  0         1s
[...]
```

## <a name="delete-a-helm-chart-from-the-repository"></a>Löschen eines Helm-Diagramms aus dem Repository

Verwenden Sie zum Löschen eines Diagramms aus dem Repository den Befehl [az acr helm delete][az-acr-helm-delete]. Geben Sie den Namen des Diagramms, z.B. *Wordpress*, und die zu löschende Version an, z.B. *2.1.10*.

```azurecli
az acr helm delete wordpress --version 2.1.10
```

Lassen Sie den Parameter `--version` aus, wenn alle Versionen des benannten Diagramms gelöscht werden sollen.

Das Diagramm wird weiterhin in `helm search <acrName>` zurückgegeben. Auch hier aktualisiert der Helm-Client nicht automatisch die Liste der in einem Repository verfügbaren Diagramme. Mithilfe des Befehls [az acr helm repo add][az-acr-helm-repo-add] können Sie den Repositoryindex des Helm-Clients aktualisieren:

```azurecli
az acr helm repo add
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde ein vorhandenes Helm-Diagramm aus dem öffentlichen Repository *stable* verwendet. Weitere Informationen zum Erstellen und Bereitstellen von Helm-Diagrammen finden Sie unter [Entwickeln von Helm-Diagrammen][develop-helm-charts].

Helm-Diagramme können als Teil des Containererstellungsprozesses verwendet werden. Weitere Informationen finden Sie unter [Verwenden von Azure Container Registry-Tasks][acr-tasks].

Weitere Informationen zum Verwenden und Verwalten von Azure Container Registry finden Sie unter [Best Practices][acr-bestpractices].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[develop-helm-charts]: https://docs.helm.sh/developing_charts/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
