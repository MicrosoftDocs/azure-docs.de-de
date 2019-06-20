---
title: Importieren von Containerimages in Azure Container Registry
description: Importieren von Containerimages in eine Azure-Containerregistrierung mithilfe von Azure-APIs, ohne dass Docker-Befehle ausgeführt werden müssen
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 02/06/2019
ms.author: danlep
ms.openlocfilehash: b8a2280fe82e0f4be8e2812f5494150927642692
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60827288"
---
# <a name="import-container-images-to-a-container-registry"></a>Importieren von Containerimages in eine Containerregistrierung

Sie können Containerimages einfach in eine Azure-Containerregistrierung importieren (kopieren), ohne Docker-Befehle verwenden zu müssen. Sie können beispielsweise Images aus einer Entwicklungsregistrierung in eine Produktionsregistrierung importieren oder Basisimages aus einer öffentlichen Registrierung kopieren.

Azure Container Registry ermöglicht einige allgemeine Szenarien zum Kopieren von Images aus einer vorhandenen Registrierung:

* Importieren aus einer öffentlichen Registrierung

* Importieren aus einer anderen Azure-Containerregistrierung im gleichen oder in einem anderen Azure-Abonnement

* Importieren aus einer Azure-fremden privaten Containerregistrierung

Der Imageimport in eine Azure-Containerregistrierung bietet gegenüber der Verwendung von Docker CLI-Befehlen die folgenden Vorteile:

* Für Ihre Clientumgebung ist keine lokale Docker-Installation erforderlich. Daher können Sie unabhängig vom unterstützten Betriebssystemtyp beliebige Containerimages importieren.

* Wenn Sie Images mit mehreren Architekturen (etwa offizielle Docker-Images) importieren, werden Images für alle Architekturen und Plattformen kopiert, die in der Manifestliste angegeben sind.

Damit Sie Containerimages importieren können, muss für diesen Artikel die Azure-Befehlszeilenschnittstelle in Azure Cloud Shell oder lokal (Version 2.0.55 oder höhere Version empfohlen) ausgeführt werden. Führen Sie `az --version` aus, um die Version zu finden. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Installieren von Azure CLI 2.0][azure-cli] Informationen dazu.

> [!NOTE]
> Azure Container Registry unterstützt darüber hinaus die [Georeplikation](container-registry-geo-replication.md), falls Sie identische Containerimages auf mehrere Azure-Regionen verteilen müssen. Durch die Georeplikation einer Registrierung (Premium-SKU erforderlich) können Sie in mehreren Regionen identische Image- und Tagnamen aus einer einzelnen Registrierung bereitstellen.
>

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie nicht bereits über eine Azure-Containerregistrierung verfügen, erstellen Sie eine Registrierung. Die hierzu erforderlichen Schritte finden Sie unter [Schnellstart: Erstellen einer privaten Containerregistrierung mit der Azure CLI](container-registry-get-started-azure-cli.md).

Um ein Image in eine Azure-Containerregistrierung importieren zu können, muss Ihre Identität über Schreibberechtigungen für die Zielregistrierung (mindestens die Rolle „Mitwirkender“) verfügen. Informationen hierzu finden Sie unter [Azure Container Registry: Rollen und Berechtigungen](container-registry-roles.md). 

## <a name="import-from-a-public-registry"></a>Importieren aus einer öffentlichen Registrierung

### <a name="import-from-docker-hub"></a>Importieren aus Docker Hub

Verwenden Sie beispielsweise den Befehl [az acr import][az-acr-import], um das Image `hello-world:latest` mit mehreren Architekturen aus Docker Hub in eine Registrierung namens *myregistry* zu importieren. `hello-world` ist ein offizielles Image von Docker Hub und befindet sich daher im `library`-Standardrepository. Fügen Sie den Repositorynamen und optional ein Tag in den Wert des Imageparameters `--source` ein. (Optional können Sie ein Image anhand seines Manifest-Digest anstelle seines Tags bestimmen. Damit wird eine bestimmte Version eines Images garantiert.)
 
```azurecli
az acr import --name myregistry --source docker.io/library/hello-world:latest --image hello-world:latest
```

Mit dem Befehl `az acr repository show-manifests` können Sie überprüfen, ob diesem Image mehrere Manifeste zugeordnet sind:

```azurecli
az acr repository show-manifests --name myregistry --repository hello-world
```

Das folgende Beispiel importiert ein öffentliches Images aus dem Repository `tensorflow` in Docker Hub:

```azurecli
az acr import --name myregistry --source docker.io/tensorflow/tensorflow:latest-gpu --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>Importieren aus der Microsoft-Containerregistrierung

Importieren Sie beispielsweise das aktuelle Windows Server Core-Image aus dem Repository `windows` in die Microsoft-Containerregistrierung.

```azurecli
az acr import --name myregistry --source mcr.microsoft.com/windows/servercore:latest --image servercore:latest
```

## <a name="import-from-another-azure-container-registry"></a>Importieren aus einer anderen Azure-Containerregistrierung

Mithilfe integrierter Azure Active Directory-Berechtigungen können Sie ein Image aus einer anderen Azure-Containerregistrierung importieren.

* Ihre Identität muss über Azure Active Directory-Berechtigungen zum Lesen aus der Quellregistrierung (Rolle „Leser“) und zum Schreiben in die Zielregistrierung (Rolle „Mitwirkender“) verfügen.

* Die Registrierung kann sich in dem gleichen oder einem anderen Azure-Abonnement im gleichen Active Directory-Mandanten befinden.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importieren aus einer Registrierung im gleichen Abonnement

Importieren Sie beispielsweise das Image `aci-helloworld:latest` aus der Quellregistrierung *mysourceregistry* in *myregistry* im gleichen Azure-Abonnement.

```azurecli
az acr import --name myregistry --source mysourceregistry.azurecr.io/aci-helloworld:latest --image hello-world:latest
```

Das folgende Beispiel importiert ein Image nicht anhand des Tags, sondern anhand des Manifest-Digest (SHA-256-Hash, dargestellt als `sha256:...`):

```azurecli
az acr import --name myregistry --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importieren aus einer Registrierung in einem anderen Abonnement

Im folgenden Beispiel befindet sich *mysourceregistry* im gleichen Active Directory-Mandanten in einem anderen Abonnement als *myregistry*. Geben Sie die Ressourcen-ID der Quellregistrierung mit dem Parameter `--registry` an. Beachten Sie, dass der Parameter `--source` nur das Quellrepository und den Imagenamen, aber nicht den Namen des Anmeldeservers für die Registrierung angibt.
 
```azurecli
az acr import --name myregistry --source sourcerepo/aci-helloworld:latest --image aci-hello-world:latest --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importieren aus einer Registrierung mit Dienstprinzipal-Anmeldeinformationen

Sie können für den Import aus einer Registrierung, auf die Sie nicht mit Active Directory-Berechtigungen zugreifen können, Anmeldeinformationen für einen Dienstprinzipal verwenden (sofern verfügbar). Geben Sie die App-ID und das Kennwort eines Active Directory-[Dienstprinzipals](container-registry-auth-service-principal.md) an, der über ACRPull-Zugriff auf die Quellregistrierung verfügt. Die Verwendung eines Dienstprinzipals empfiehlt sich für Buildsysteme und andere unbeaufsichtigte Systeme, die Images in Ihre Registrierung importieren müssen.

```azurecli
az acr import --name myregistry --source sourceregistry.azurecr.io/sourcerepo/sourceimage:tag --image targetimage:tag --username <SP_App_ID> –-password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importieren aus einer Azure-fremden privaten Containerregistrierung

Importieren Sie ein Image aus einer privaten Registrierung, indem Sie Anmeldeinformationen angeben, die den Pullzugriff auf die Registrierung ermöglichen. Rufen Sie beispielsweise ein Image per Pull aus einer privaten Docker-Registrierung ab: 

```azurecli
az acr import --name myregistry --source docker.io/sourcerepo/sourceimage:tag --image sourceimage:tag --username <username> --password <password>
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Containerimages aus einer öffentlichen Registrierung oder einer anderen privaten Registrierung in eine Azure-Containerregistrierung importieren. Informationen zu weiteren Optionen zum Importieren von Images finden Sie in der [az acr import][az-acr-import]-Befehlsreferenz. 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
