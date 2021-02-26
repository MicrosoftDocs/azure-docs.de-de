---
title: Importieren von Containerimages
description: Importieren von Containerimages in eine Azure-Containerregistrierung mithilfe von Azure-APIs, ohne dass Docker-Befehle ausgeführt werden müssen
ms.topic: article
ms.date: 01/15/2021
ms.openlocfilehash: e6976f854b449f68faedd51878c2f3a7fe75cb0f
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988235"
---
# <a name="import-container-images-to-a-container-registry"></a>Importieren von Containerimages in eine Containerregistrierung

Sie können Containerimages einfach in eine Azure-Containerregistrierung importieren (kopieren), ohne Docker-Befehle verwenden zu müssen. Sie können beispielsweise Images aus einer Entwicklungsregistrierung in eine Produktionsregistrierung importieren oder Basisimages aus einer öffentlichen Registrierung kopieren.

Azure Container Registry ermöglicht einige allgemeine Szenarien zum Kopieren von Images aus einer vorhandenen Registrierung:

* Importieren aus einer öffentlichen Registrierung

* Importieren von Images aus einer anderen Azure-Containerregistrierung im selben oder in einem anderen Azure-Abonnement oder -Mandanten

* Importieren aus einer Azure-fremden privaten Containerregistrierung

Der Imageimport in eine Azure-Containerregistrierung bietet gegenüber der Verwendung von Docker CLI-Befehlen die folgenden Vorteile:

* Für Ihre Clientumgebung ist keine lokale Docker-Installation erforderlich. Daher können Sie unabhängig vom unterstützten Betriebssystemtyp beliebige Containerimages importieren.

* Wenn Sie Images mit mehreren Architekturen (etwa offizielle Docker-Images) importieren, werden Images für alle Architekturen und Plattformen kopiert, die in der Manifestliste angegeben sind.

* Der Zugriff auf die Zielregistrierung muss nicht über den öffentlichen Endpunkt der Registrierung erfolgen.

Damit Sie Containerimages importieren können, muss für diesen Artikel die Azure-Befehlszeilenschnittstelle in Azure Cloud Shell oder lokal (Version 2.0.55 oder höhere Version empfohlen) ausgeführt werden. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli].

> [!NOTE]
> Azure Container Registry unterstützt darüber hinaus die [Georeplikation](container-registry-geo-replication.md), falls Sie identische Containerimages auf mehrere Azure-Regionen verteilen müssen. Durch die Georeplikation einer Registrierung (Premium-Dienstebene erforderlich) können Sie in mehreren Regionen identische Image- und Tagnamen aus einer einzelnen Registrierung bereitstellen.
>

> [!IMPORTANT]
> Änderungen am Imageimport zwischen zwei Azure Container Registry-Instanzen zum Januar 2021:
> * Für den Import in eine Azure Container Registry-Instanz mit Netzwerkeinschränkungen oder aus dieser muss die eingeschränkte Registrierung das Umgehen des Netzwerks für den [**Zugriff durch vertrauenswürdige Dienste erlauben**](allow-access-trusted-services.md). Standardmäßig ist die Einstellung aktiviert und ermöglicht den Import. Wenn die Einstellung in einer neu erstellten Registrierung mit einem privaten Endpunkt oder mit Firewallregeln für die Registrierung nicht aktiviert ist, tritt beim Import ein Fehler auf. 
> * Bei einer vorhandenen Azure Container Registry-Instanz mit Netzwerkeinschränkungen, die als Importquelle oder -ziel verwendet wird, ist die Aktivierung dieser Netzwerksicherheitsfunktion optional, wird jedoch empfohlen.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie nicht bereits über eine Azure-Containerregistrierung verfügen, erstellen Sie eine Registrierung. Die hierzu erforderlichen Schritte finden Sie unter [Schnellstart: Erstellen einer privaten Containerregistrierung mit der Azure CLI](container-registry-get-started-azure-cli.md).

Um ein Image in eine Azure-Containerregistrierung importieren zu können, muss Ihre Identität über Schreibberechtigungen für die Zielregistrierung (mindestens die Rolle „Mitwirkender“ oder eine benutzerdefinierte Rolle, die die importImage-Aktion zulässt) verfügen. Informationen hierzu finden Sie unter [Azure Container Registry: Rollen und Berechtigungen](container-registry-roles.md#custom-roles). 

## <a name="import-from-a-public-registry"></a>Importieren aus einer öffentlichen Registrierung

### <a name="import-from-docker-hub"></a>Importieren aus Docker Hub

Verwenden Sie beispielsweise den Befehl [az acr import][az-acr-import], um das Image `hello-world:latest` mit mehreren Architekturen aus Docker Hub in eine Registrierung namens *myregistry* zu importieren. `hello-world` ist ein offizielles Image von Docker Hub und befindet sich daher im `library`-Standardrepository. Fügen Sie den Repositorynamen und optional ein Tag in den Wert des Imageparameters `--source` ein. (Optional können Sie ein Image anhand seines Manifest-Digest anstelle seines Tags bestimmen. Damit wird eine bestimmte Version eines Images garantiert.)
 
```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

Mit dem Befehl `az acr repository show-manifests` können Sie überprüfen, ob diesem Image mehrere Manifeste zugeordnet sind:

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

Wenn Sie ein [Docker Hub-Konto](https://www.docker.com/pricing) haben, sollten Sie beim Importieren eines Images aus Docker Hub diese Anmeldeinformationen verwenden. Übergeben Sie den Docker Hub-Benutzernamen mit dem zugehörigen Kennwort oder ein [persönliches Zugriffstoken](https://docs.docker.com/docker-hub/access-tokens/) als Parameter an `az acr import`. Im folgenden Beispiel wird ein öffentliches Image aus dem Repository `tensorflow` mithilfe von Docker Hub-Anmeldeinformationen in Docker Hub importiert:

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
  --username <Docker Hub user name>
  --password <Docker Hub token>
```

### <a name="import-from-microsoft-container-registry"></a>Importieren aus der Microsoft-Containerregistrierung

Importieren Sie beispielsweise das Windows Server Core-Image `ltsc2019` aus dem Repository `windows` in die Microsoft Container Registry.

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:ltsc2019 \
--image servercore:ltsc2019
```

## <a name="import-from-an-azure-container-registry-in-the-same-ad-tenant"></a>Importieren von Images aus einer Azure-Containerregistrierung im selben AD-Mandanten

Mithilfe von integrierten Azure Active Directory-Berechtigungen können Sie ein Image aus einer Azure-Containerregistrierung im selben AD-Mandanten importieren.

* Ihre Identität muss über Azure Active Directory-Berechtigungen zum Lesen aus der Quellregistrierung (Rolle „Leser“) und zum Importieren in die Zielregistrierung (Rolle „Mitwirkender“ bzw. [benutzerdefinierte Rolle](container-registry-roles.md#custom-roles), die die importImage-Aktion zulässt) verfügen.

* Die Registrierung kann sich in dem gleichen oder einem anderen Azure-Abonnement im gleichen Active Directory-Mandanten befinden.

* Der [öffentliche Zugriff](container-registry-access-selected-networks.md#disable-public-network-access) auf die Quellregistrierung kann deaktiviert werden. Wenn der öffentliche Zugriff deaktiviert ist, geben Sie die Quellregistrierung nach Ressourcen-ID anstelle des Servernamens der Registrierungsanmeldung an.

* Wenn die Quell- oder Zielregistrierung über einen privaten Endpunkt verfügt oder Registrierungsfirewallregeln angewandt werden, stellen Sie sicher, dass die eingeschränkte Registrierung den Zugriff auf das Netzwerk [für vertrauenswürdige Dienste zulässt](allow-access-trusted-services.md).

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importieren aus einer Registrierung im gleichen Abonnement

Importieren Sie beispielsweise das Image `aci-helloworld:latest` aus der Quellregistrierung *mysourceregistry* in *myregistry* im gleichen Azure-Abonnement.

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

Das folgende Beispiel importiert das `aci-helloworld:latest`-Image in *myregistry* aus einer Quellregistrierung *mysourceregistry*, in der der Zugriff auf den öffentlichen Endpunkt der Registrierung deaktiviert ist. Geben Sie die Ressourcen-ID der Quellregistrierung mit dem Parameter `--registry` an. Beachten Sie, dass der Parameter `--source` nur das Quellrepository und Tag, aber nicht den Namen des Anmeldeservers für die Registrierung angibt.

```azurecli
az acr import \
  --name myregistry \
  --source aci-helloworld:latest \
  --image aci-helloworld:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

Das folgende Beispiel importiert ein Image nicht anhand des Tags, sondern anhand des Manifest-Digest (SHA-256-Hash, dargestellt als `sha256:...`):

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importieren aus einer Registrierung in einem anderen Abonnement

Im folgenden Beispiel befindet sich *mysourceregistry* im gleichen Active Directory-Mandanten in einem anderen Abonnement als *myregistry*. Geben Sie die Ressourcen-ID der Quellregistrierung mit dem Parameter `--registry` an. Beachten Sie, dass der Parameter `--source` nur das Quellrepository und Tag, aber nicht den Namen des Anmeldeservers für die Registrierung angibt.

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importieren aus einer Registrierung mit Dienstprinzipal-Anmeldeinformationen

Sie können für den Import aus einer Registrierung, auf die Sie nicht mit integrierten Active Directory-Berechtigungen zugreifen können, Anmeldeinformationen für einen Dienstprinzipal für die Quellregistrierung verwenden (sofern verfügbar). Geben Sie die App-ID und das Kennwort eines Active Directory-[Dienstprinzipals](container-registry-auth-service-principal.md) an, der über ACRPull-Zugriff auf die Quellregistrierung verfügt. Die Verwendung eines Dienstprinzipals empfiehlt sich für Buildsysteme und andere unbeaufsichtigte Systeme, die Images in Ihre Registrierung importieren müssen.

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-an-azure-container-registry-in-a-different-ad-tenant"></a>Importieren von Images aus einer Azure-Containerregistrierung in einem anderen AD-Mandanten

Wenn Sie Images aus einer Azure-Containerregistrierung in einem anderen Azure Active Directory-Mandanten importieren möchten, geben Sie die Quellregistrierung über den Anmeldeservernamen an sowie den Benutzernamen und das Kennwort für Pullzugriff auf die Registrierung. Verwenden Sie z. B. ein [Token mit Repositorygültigkeitsbereich](container-registry-repository-scoped-permissions.md) und ein Kennwort oder die App-ID und das Kennwort für einen Active Directory-[Dienstprinzipal](container-registry-auth-service-principal.md), der über ACRPull-Zugriff auf die Quellregistrierung verfügt. 

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importieren aus einer Azure-fremden privaten Containerregistrierung

Importieren Sie ein Image aus einer privaten Registrierung außerhalb von Azure, indem Sie Anmeldeinformationen angeben, die den Pullzugriff auf die Registrierung ermöglichen. Rufen Sie beispielsweise ein Image per Pull aus einer privaten Docker-Registrierung ab: 

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Containerimages aus einer öffentlichen Registrierung oder einer anderen privaten Registrierung in eine Azure-Containerregistrierung importieren. Informationen zu weiteren Optionen beim Importieren von Images finden Sie in der Befehlsreferenz für [az acr import][az-acr-import]. 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
