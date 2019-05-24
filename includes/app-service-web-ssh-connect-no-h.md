---
title: Includedatei
description: Includedatei
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 060bc1039982cc0a77214d5dbe2a08de7a839c84
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66138228"
---
Um eine direkte SSH-Sitzung mit Ihrem Container zu öffnen, sollte Ihre App ausgeführt werden.

Fügen Sie die folgende URL in Ihren Browser ein, und ersetzen Sie \<App-Name> durch den Namen Ihrer App:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Wenn Sie noch nicht authentifiziert sind, müssen Sie sich mit Ihrem Azure-Abonnement für die Verbindung authentifizieren. Nach der Authentifizierung wird im Browser eine Shell angezeigt, über die Sie Befehle innerhalb Ihres Containers ausführen können.

![SSH-Verbindung](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
