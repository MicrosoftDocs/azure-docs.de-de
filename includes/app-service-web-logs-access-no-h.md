---
title: include file
description: include file
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 0dd6618bdee8e6810d414d4b04b16a1e0a9c90ed
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178567"
---
Sie können auf die Konsolenprotokolle zugreifen, die aus dem Container generiert werden. Aktivieren Sie zuerst die Containerprotokollierung, indem Sie in Cloud Shell den folgenden Befehl ausführen:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

Führen Sie den folgenden Befehl aus, um den Protokolldatenstrom anzuzeigen, nachdem die Containerprotokollierung aktiviert wurde:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Falls Sie nicht sofort Konsolenprotokolle sehen, können Sie es nach 30 Sekunden noch einmal versuchen.

> [!NOTE]
> Sie können die Protokolldateien auch im Browsen unter `https://<app-name>.scm.azurewebsites.net/api/logs/docker` untersuchen.

Um das Protokollstreaming jederzeit zu beenden, geben Sie `Ctrl`+`C` ein.
