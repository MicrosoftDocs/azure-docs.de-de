---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 02beb6bdce096c35d8948cc8aefab6cc97be907c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063932"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Authentifizieren einer privaten Containerregistrierung mit der Docker-Befehlszeilenschnittstelle

Es gibt mehrere Möglichkeiten, sich bei der privaten Containerregistrierung für Cognitive Services-Container zu authentifizieren, aber die empfohlene Methode über die Befehlszeile ist die Verwendung der [Docker-CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Verwenden Sie den [`docker login`-Befehl](https://docs.docker.com/engine/reference/commandline/login/) wie im folgenden Beispiel gezeigt, um sich bei `containerpreview.azurecr.io` (der privaten Containerregistrierung für Cognitive Services-Container) anzumelden. Ersetzen Sie *\<username\>* durch den Benutzernamen und *\<password\>* durch das Kennwort. Diese Anmeldeinformationen wurden Ihnen vom Azure Cognitive Services-Team bereitgestellt.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Wenn Sie Ihre Anmeldeinformationen in einer Textdatei gesichert haben, können Sie den Inhalt dieser Textdatei über den Befehl `cat` mit dem Befehl `docker login` verketten, wie im folgenden Beispiel gezeigt. Ersetzen Sie *\<passwordFile\>* durch den Pfad und den Namen der Textdatei mit dem Kennwort und *\<username\>* durch den Benutzernamen, der in Ihren Anmeldeinformationen angegeben wurde.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
