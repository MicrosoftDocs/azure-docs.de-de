---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 02/11/2019
ms.openlocfilehash: 480cec39e42e075ad83ad3e32b88c638094db64f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178367"
---
> [!TIP]
> Mithilfe des Befehls [docker images](https://docs.docker.com/engine/reference/commandline/images/) können Sie Ihre heruntergeladenen Containerimages auflisten. Mit dem folgenden Befehl werden beispielsweise die ID, das Repository und das Tag jedes heruntergeladenen Containerimages in Form einer Tabelle aufgelistet:
>
>  ```
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>
>  IMAGE ID            REPOSITORY              TAG
>  ebbee78a6baa       <container-name>         latest
>  ``` 