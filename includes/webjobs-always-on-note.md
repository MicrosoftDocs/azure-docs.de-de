---
title: include file
description: include file
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c255be53a1809bf5dd3fc6b184852767dfec9c66
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009675"
---
> [!NOTE]
> Nach 20 Minuten Inaktivität kann bei einer Web-App ein Timeout auftreten. Der Zeitgeber kann nur durch Anforderungen an die eigentliche Web-App zurückgesetzt werden. Das Anzeigen der Konfiguration der App im Azure-Portal oder das Senden von Anforderungen an die Website mit erweiterten Tools (`https://<app_name>.scm.azurewebsites.net`) führt nicht zum Zurücksetzen des Zeitgebers. Wenn Sie Ihre Web-App zur Ausführung kontinuierlicher oder geplanter (Zeitgebertrigger-)Webaufträge konfigurieren, aktivieren Sie auf der Seite mit der Azure-**Konfiguration** Ihrer Web-App die Einstellung **Always On**, um die zuverlässige Ausführung der Webaufträge sicherzustellen. Dieses Feature steht nur in den [Tarifen](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) „Basic“, „Standard“ und „Premium“ zur Verfügung.
