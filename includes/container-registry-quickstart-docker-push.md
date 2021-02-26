---
title: include file
description: include file
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 08/04/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 404fd10c3e3610671d2b6e5dbc6aba8bcaa70046
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100019259"
---
## <a name="push-image-to-registry"></a>Pushen eines Image in die Registrierung

Um ein Image mithilfe von Push an Ihre Azure Container Registry-Instanz übertragen zu können, benötigen Sie zunächst ein Image. Wenn Sie noch nicht über lokale Containerimages verfügen, führen Sie den folgenden [docker pull][docker-pull]-Befehl aus, um ein vorhandenes öffentliches Image abzurufen. In diesem Beispiel wird das Image `hello-world` aus Microsoft Container Registry gepullt.

```
docker pull mcr.microsoft.com/hello-world
```

Bevor Sie ein Image mithilfe von Push in Ihre Registrierung übertragen können, müssen Sie es mit dem vollqualifizierten Namen des Anmeldeservers Ihrer Registrierungsinstanz markieren. Der Name des Anmeldeservers wird im Format *\<registry-name\>.azurecr.io* (nur Kleinbuchstaben) angegeben, z. B. *mycontainerregistry.azurecr.io*.

Markieren Sie das Image mithilfe des Befehls [docker tag][docker-tag]. Ersetzen Sie `<login-server>` durch den Anmeldeservernamen Ihrer ACR-Instanz.

```
docker tag mcr.microsoft.com/hello-world <login-server>/hello-world:v1
```

Beispiel:

```
docker tag mcr.microsoft.com/hello-world mycontainerregistry.azurecr.io/hello-world:v1
```


Nun können Sie das Image mit [docker push][docker-push] per Pushvorgang an die Registrierungsinstanz übertragen. Ersetzen Sie `<login-server>` durch den Anmeldeservernamen Ihrer Registrierungsinstanz. In diesem Beispiel wird das Repository **hello-world** mit dem Image `hello-world:v1` erstellt.

```
docker push <login-server>/hello-world:v1
```

Nachdem das Image in Ihre Containerregistrierung gepusht wurde, entfernen Sie das Image `hello-world:v1` aus Ihrer lokalen Docker-Umgebung. (Beachten Sie, dass der Befehl [docker rmi][docker-rmi] nicht das Image aus dem Repository **hello-world** in Ihrer Azure-Containerregistrierung entfernt.)

```
docker rmi <login-server>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->

