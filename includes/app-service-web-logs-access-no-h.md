---
title: include file
description: include file
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 7803ac9009af1657e7f162d656898492a694e28f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997914"
---
Aktivieren Sie die Diagnoseprotokollierung, indem Sie den folgenden Befehl in [Cloud Shell](https://shell.azure.com)ausführen, um auf die Konsolenprotokolle zuzugreifen, die innerhalb des Anwendungscodes in App Service generiert wurden:

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --application-logging true --level Verbose
```

Mögliche Werte für `--level` sind `Error`, `Warning` `Info` oder `Verbose`. Jede nachfolgende Ebene enthält die vorherige Ebene. Beispiel: `Error` enthält nur Fehlermeldungen, und `Verbose` enthält alle Meldungen.

Führen Sie den folgenden Befehl aus, um den Protokolldatenstrom anzuzeigen, nachdem die Diagnoseprotokollierung aktiviert wurde:

```azurecli-interactive
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

Falls Sie nicht sofort Konsolenprotokolle sehen, können Sie es nach 30 Sekunden noch einmal versuchen.

> [!NOTE]
> Sie können die Protokolldateien auch im Browser unter `https://<app-name>.scm.azurewebsites.net/api/logs/docker` untersuchen.

Um das Protokollstreaming jederzeit zu beenden, geben Sie `Ctrl`+`C` ein.
