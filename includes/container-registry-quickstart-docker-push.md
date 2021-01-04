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
ms.openlocfilehash: 6a16106495e584c9acbc02d380242df665f35ce5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020001"
---
## <a name="push-image-to-registry"></a>Pushen eines Image in die Registrierung

Um ein Image mithilfe von Push an Ihre Azure Container Registry-Instanz übertragen zu können, benötigen Sie zunächst ein Image. Wenn Sie noch nicht über lokale Containerimages verfügen, führen Sie den folgenden [docker pull][docker-pull]-Befehl aus, um ein vorhandenes Image aus Docker Hub abzurufen. Pullen Sie für dieses Beispiel das Image `hello-world`.

```
docker pull hello-world
```

Bevor Sie ein Image mithilfe von Push in Ihre Registrierung übertragen können, müssen Sie es mit dem vollqualifizierten Namen des Anmeldeservers Ihrer Registrierungsinstanz markieren. Der Name des Anmeldeservers wird im Format *\<registry-name\>.azurecr.io* (nur Kleinbuchstaben) angegeben, z. B. *mycontainerregistry.azurecr.io*.

Markieren Sie das Image mithilfe des Befehls [docker tag][docker-tag]. Ersetzen Sie `<login-server>` durch den Anmeldeservernamen Ihrer ACR-Instanz.

```
docker tag hello-world <login-server>/hello-world:v1
```

Beispiel:

```
docker tag hello-world mycontainerregistry.azurecr.io/hello-world:v1
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

